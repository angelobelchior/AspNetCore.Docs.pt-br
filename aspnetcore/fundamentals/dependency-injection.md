---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 2d002e075f9d57654589b540e522307c363d9660
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153539"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="435e8-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="435e8-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="435e8-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="435e8-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="435e8-105">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="435e8-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="435e8-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="435e8-107">Para obter mais informações sobre injeção de dependência de opções, consulte <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="435e8-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="435e8-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="435e8-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="435e8-109">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="435e8-109">Overview of dependency injection</span></span>

<span data-ttu-id="435e8-110">Uma *dependência* é um objeto do qual outro objeto depende.</span><span class="sxs-lookup"><span data-stu-id="435e8-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="435e8-111">Examine a seguinte `MyDependency` classe com um `WriteMessage` método de que outras classes dependem:</span><span class="sxs-lookup"><span data-stu-id="435e8-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="435e8-112">Uma classe pode criar uma instância da `MyDependency` classe para fazer uso de seu `WriteMessage` método.</span><span class="sxs-lookup"><span data-stu-id="435e8-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="435e8-113">No exemplo a seguir, a `MyDependency` classe é uma dependência da `IndexModel` classe:</span><span class="sxs-lookup"><span data-stu-id="435e8-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="435e8-114">A classe cria e depende diretamente da `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="435e8-115">As dependências de código, como no exemplo anterior, são problemáticas e devem ser evitadas pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="435e8-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="435e8-116">Para substituir `MyDependency` por uma implementação diferente, a `IndexModel` classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="435e8-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="435e8-117">Se `MyDependency` tiver dependências, elas também deverão ser configuradas pela `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="435e8-118">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="435e8-119">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="435e8-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="435e8-120">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="435e8-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="435e8-121">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="435e8-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="435e8-122">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="435e8-123">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="435e8-124">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="435e8-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="435e8-125">Normalmente, os serviços são registrados no método do aplicativo `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="435e8-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="435e8-126">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="435e8-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="435e8-127">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="435e8-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="435e8-128">No [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a `IMyDependency` interface define o `WriteMessage` método:</span><span class="sxs-lookup"><span data-stu-id="435e8-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="435e8-129">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="435e8-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="435e8-130">O aplicativo de exemplo registra o `IMyDependency` serviço com o tipo concreto `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="435e8-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="435e8-131">O <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> método registra o serviço com um tempo de vida de escopo, o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="435e8-132">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="435e8-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="435e8-133">No aplicativo de exemplo, o `IMyDependency` serviço é solicitado e usado para chamar o `WriteMessage` método:</span><span class="sxs-lookup"><span data-stu-id="435e8-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="435e8-134">Usando o padrão DI, o controlador:</span><span class="sxs-lookup"><span data-stu-id="435e8-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="435e8-135">Não usa o tipo concreto `MyDependency` , apenas a `IMyDependency` interface que ele implementa.</span><span class="sxs-lookup"><span data-stu-id="435e8-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="435e8-136">Isso facilita a alteração da implementação que o controlador usa sem modificar o controlador.</span><span class="sxs-lookup"><span data-stu-id="435e8-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="435e8-137">Não cria uma instância do `MyDependency` , ela é criada pelo contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="435e8-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="435e8-138">A implementação da `IMyDependency` interface pode ser aprimorada usando a API de registro em log interna:</span><span class="sxs-lookup"><span data-stu-id="435e8-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="435e8-139">O `ConfigureServices` método atualizado registra a nova `IMyDependency` implementação:</span><span class="sxs-lookup"><span data-stu-id="435e8-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="435e8-140">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601> , que ele solicita no construtor.</span><span class="sxs-lookup"><span data-stu-id="435e8-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="435e8-141">`ILogger<TCategoryName>` é um [serviço fornecido pelo Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="435e8-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="435e8-142">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="435e8-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="435e8-143">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="435e8-144">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="435e8-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="435e8-145">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="435e8-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="435e8-146">O contêiner resolve aproveitando `ILogger<TCategoryName>` os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar cada [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="435e8-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="435e8-147">Na terminologia de injeção de dependência, um serviço:</span><span class="sxs-lookup"><span data-stu-id="435e8-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="435e8-148">Normalmente é um objeto que fornece um serviço para outros objetos, como o `IMyDependency` serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="435e8-149">O não está relacionado a um serviço Web, embora o serviço possa usar um serviço Web.</span><span class="sxs-lookup"><span data-stu-id="435e8-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="435e8-150">A estrutura fornece um sistema de [registro em log](xref:fundamentals/logging/index) robusto.</span><span class="sxs-lookup"><span data-stu-id="435e8-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="435e8-151">As `IMyDependency` implementações mostradas nos exemplos anteriores foram escritas para demonstrar a di básica, não para implementar o registro em log.</span><span class="sxs-lookup"><span data-stu-id="435e8-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="435e8-152">A maioria dos aplicativos não deve precisar gravar agentes.</span><span class="sxs-lookup"><span data-stu-id="435e8-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="435e8-153">O código a seguir demonstra como usar o log padrão, que não exige que nenhum serviço seja registrado no `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="435e8-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="435e8-154">Usando o código anterior, não há necessidade de atualizar `ConfigureServices` , porque o [registro em log](xref:fundamentals/logging/index) é fornecido pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="435e8-155">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="435e8-155">Services injected into Startup</span></span>

<span data-ttu-id="435e8-156">Os serviços podem ser injetados no `Startup` Construtor e no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="435e8-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="435e8-157">Somente os seguintes serviços podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="435e8-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="435e8-158">Qualquer serviço registrado com o contêiner DI pode ser injetado no `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="435e8-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="435e8-159">Para obter mais informações, consulte <xref:fundamentals/startup> e [acessar a configuração na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="435e8-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="435e8-160">Registrar grupos de serviços com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="435e8-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="435e8-161">O ASP.NET Core Framework usa uma Convenção para registrar um grupo de serviços relacionados.</span><span class="sxs-lookup"><span data-stu-id="435e8-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="435e8-162">A Convenção é usar um método de `Add{GROUP_NAME}` extensão único para registrar todos os serviços exigidos por um recurso de estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="435e8-163">Por exemplo, o <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. addcontrollers> método de extensão registra os serviços necessários para controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="435e8-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="435e8-164">O código a seguir é gerado pelo Razor modelo de páginas usando contas de usuário individuais e mostra como adicionar serviços adicionais ao contêiner usando os métodos de extensão <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="435e8-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="435e8-165">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-165">Service lifetimes</span></span>

<span data-ttu-id="435e8-166">Os serviços podem ser registrados com um dos seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="435e8-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="435e8-167">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-167">Transient</span></span>
* <span data-ttu-id="435e8-168">Com escopo</span><span class="sxs-lookup"><span data-stu-id="435e8-168">Scoped</span></span>
* <span data-ttu-id="435e8-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-169">Singleton</span></span>

<span data-ttu-id="435e8-170">As seções a seguir descrevem cada um dos tempos de vida anteriores.</span><span class="sxs-lookup"><span data-stu-id="435e8-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="435e8-171">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="435e8-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="435e8-172">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-172">Transient</span></span>

<span data-ttu-id="435e8-173">Serviços temporários de tempo de vida são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="435e8-174">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="435e8-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="435e8-175">Registre serviços transitórios com <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="435e8-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="435e8-176">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="435e8-177">Com escopo</span><span class="sxs-lookup"><span data-stu-id="435e8-177">Scoped</span></span>

<span data-ttu-id="435e8-178">Os serviços com tempo de vida com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="435e8-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="435e8-179">Registre os serviços com escopo com <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="435e8-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="435e8-180">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="435e8-181">Ao usar Entity Framework Core, o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> método de extensão registra os `DbContext` tipos com um tempo de vida com escopo definido por padrão.</span><span class="sxs-lookup"><span data-stu-id="435e8-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="435e8-182">***Não*** resolva um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="435e8-183">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="435e8-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="435e8-184">Não há problema em:</span><span class="sxs-lookup"><span data-stu-id="435e8-184">It's fine to:</span></span>

* <span data-ttu-id="435e8-185">Resolva um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="435e8-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="435e8-186">Resolva um serviço com escopo de outro serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="435e8-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="435e8-187">Por padrão, no ambiente de desenvolvimento, a resolução de um serviço de outro serviço com um tempo de vida maior gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="435e8-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="435e8-188">Para obter mais informações, confira [Validação de escopo](#sv).</span><span class="sxs-lookup"><span data-stu-id="435e8-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="435e8-189">Para usar serviços com escopo no middleware, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="435e8-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="435e8-190">Injetar o serviço no `Invoke` método ou do middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="435e8-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="435e8-191">O uso de [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) gera uma exceção de tempo de execução porque força o serviço de escopo a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="435e8-192">O exemplo na seção de [Opções de tempo de vida e de registro](#lifetime-and-registration-options) demonstra a `InvokeAsync` abordagem.</span><span class="sxs-lookup"><span data-stu-id="435e8-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="435e8-193">Use o [middleware baseado em fábrica](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="435e8-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="435e8-194">O middleware registrado usando essa abordagem é ativado por solicitação do cliente (conexão), que permite que os serviços com escopo sejam injetados no método do middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="435e8-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="435e8-195">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="435e8-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="435e8-196">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-196">Singleton</span></span>

<span data-ttu-id="435e8-197">Os serviços de vida útil singleton são criados:</span><span class="sxs-lookup"><span data-stu-id="435e8-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="435e8-198">Na primeira vez que forem solicitadas.</span><span class="sxs-lookup"><span data-stu-id="435e8-198">The first time they're requested.</span></span>
* <span data-ttu-id="435e8-199">Pelo desenvolvedor, ao fornecer uma instância de implementação diretamente para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="435e8-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="435e8-200">Essa abordagem raramente é necessária.</span><span class="sxs-lookup"><span data-stu-id="435e8-200">This approach is rarely needed.</span></span>

<span data-ttu-id="435e8-201">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="435e8-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="435e8-202">Se o aplicativo exigir um comportamento singleton, permita que o contêiner de serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="435e8-203">Não implemente o padrão de design singleton e forneça código para descartar o singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="435e8-204">Os serviços nunca devem ser descartados pelo código que resolveu o serviço do contêiner.</span><span class="sxs-lookup"><span data-stu-id="435e8-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="435e8-205">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="435e8-206">Registre os serviços singleton com <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="435e8-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="435e8-207">Os serviços singleton devem ser thread-safe e geralmente são usados em serviços sem estado.</span><span class="sxs-lookup"><span data-stu-id="435e8-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="435e8-208">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="435e8-209">Como a memória não é liberada até que o aplicativo seja desligado, considere o uso de memória com um serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="435e8-210">***Não*** resolva um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="435e8-211">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="435e8-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="435e8-212">É bom resolver um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="435e8-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="435e8-213">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-213">Service registration methods</span></span>

<span data-ttu-id="435e8-214">A estrutura fornece métodos de extensão de registro de serviço que são úteis em cenários específicos:</span><span class="sxs-lookup"><span data-stu-id="435e8-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="435e8-215">Método</span><span class="sxs-lookup"><span data-stu-id="435e8-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="435e8-216">Automática</span><span class="sxs-lookup"><span data-stu-id="435e8-216">Automatic</span></span><br><span data-ttu-id="435e8-217">objeto</span><span class="sxs-lookup"><span data-stu-id="435e8-217">object</span></span><br><span data-ttu-id="435e8-218">descarte</span><span class="sxs-lookup"><span data-stu-id="435e8-218">disposal</span></span> | <span data-ttu-id="435e8-219">Vários</span><span class="sxs-lookup"><span data-stu-id="435e8-219">Multiple</span></span><br><span data-ttu-id="435e8-220">implementações</span><span class="sxs-lookup"><span data-stu-id="435e8-220">implementations</span></span> | <span data-ttu-id="435e8-221">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="435e8-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="435e8-222">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="435e8-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="435e8-223">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-223">Yes</span></span>                             | <span data-ttu-id="435e8-224">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-224">Yes</span></span>                         | <span data-ttu-id="435e8-225">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="435e8-226">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="435e8-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="435e8-227">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-227">Yes</span></span>                             | <span data-ttu-id="435e8-228">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-228">Yes</span></span>                         | <span data-ttu-id="435e8-229">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="435e8-230">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="435e8-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="435e8-231">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-231">Yes</span></span>                             | <span data-ttu-id="435e8-232">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-232">No</span></span>                          | <span data-ttu-id="435e8-233">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="435e8-234">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="435e8-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="435e8-235">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-235">No</span></span>                              | <span data-ttu-id="435e8-236">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-236">Yes</span></span>                         | <span data-ttu-id="435e8-237">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="435e8-238">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="435e8-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="435e8-239">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-239">No</span></span>                              | <span data-ttu-id="435e8-240">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-240">No</span></span>                          | <span data-ttu-id="435e8-241">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-241">Yes</span></span>       |

<span data-ttu-id="435e8-242">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="435e8-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="435e8-243">É comum usar várias implementações ao [simular tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="435e8-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="435e8-244">A estrutura também fornece `TryAdd{LIFETIME}` métodos de extensão, que registram o serviço somente se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="435e8-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="435e8-245">No exemplo a seguir, a chamada para `AddSingleton` registra `MyDependency` como uma implementação para `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="435e8-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="435e8-246">A chamada para `TryAddSingleton` não tem efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="435e8-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="435e8-247">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="435e8-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="435e8-248">Os métodos [TryAddEnumerable (Service Descriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registram o serviço somente se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="435e8-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="435e8-249">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="435e8-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="435e8-250">Ao registrar serviços, o desenvolvedor deve adicionar uma instância se um do mesmo tipo ainda não tiver sido adicionado.</span><span class="sxs-lookup"><span data-stu-id="435e8-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="435e8-251">Em geral, os autores `TryAddEnumerable` de biblioteca usam para evitar o registro de várias cópias de uma implementação no contêiner.</span><span class="sxs-lookup"><span data-stu-id="435e8-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="435e8-252">No exemplo a seguir, a primeira chamada para `TryAddEnumerable` registra `MyDependency` como uma implementação para `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="435e8-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="435e8-253">A segunda chamada é registrada `MyDependency` para `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="435e8-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="435e8-254">A terceira chamada não tem nenhum efeito porque `IMyDependency1` já tem uma implementação registrada de `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="435e8-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="435e8-255">O registro de serviço geralmente é independente de ordem, exceto ao registrar várias implementações do mesmo tipo.</span><span class="sxs-lookup"><span data-stu-id="435e8-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="435e8-256">`IServiceCollection` é uma coleção de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objetos.</span><span class="sxs-lookup"><span data-stu-id="435e8-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="435e8-257">O exemplo a seguir mostra como registrar um serviço criando e adicionando um `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="435e8-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="435e8-258">Os `Add{LIFETIME}` métodos internos usam a mesma abordagem.</span><span class="sxs-lookup"><span data-stu-id="435e8-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="435e8-259">Por exemplo, consulte o [código-fonte de Addscoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="435e8-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="435e8-260">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="435e8-260">Constructor injection behavior</span></span>

<span data-ttu-id="435e8-261">Os serviços podem ser resolvidos usando:</span><span class="sxs-lookup"><span data-stu-id="435e8-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="435e8-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="435e8-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="435e8-263">Cria objetos que não estão registrados no contêiner.</span><span class="sxs-lookup"><span data-stu-id="435e8-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="435e8-264">Usado com recursos de estrutura, como [auxiliares de marca](xref:mvc/views/tag-helpers/intro), controladores MVC e [ASSOCIADORES de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="435e8-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="435e8-265">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="435e8-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="435e8-266">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="435e8-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="435e8-267">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="435e8-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="435e8-268">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="435e8-269">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="435e8-269">Entity Framework contexts</span></span>

<span data-ttu-id="435e8-270">Por padrão, os contextos de Entity Framework são adicionados ao contêiner de serviço usando o [tempo de vida do escopo](#service-lifetimes) porque as operações de banco de dados do aplicativo Web normalmente são delimitadas à solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="435e8-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="435e8-271">Para usar um tempo de vida diferente, especifique o tempo de vida usando uma <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="435e8-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="435e8-272">Os serviços de um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida menor do que o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="435e8-273">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="435e8-273">Lifetime and registration options</span></span>

<span data-ttu-id="435e8-274">Para demonstrar a diferença entre tempos de vida de serviço e suas opções de registro, considere as seguintes interfaces que representam uma tarefa como uma operação com um identificador, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="435e8-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="435e8-275">Dependendo de como o tempo de vida do serviço de uma operação está configurado para as seguintes interfaces, o contêiner fornecerá as mesmas instâncias do serviço ou as mesmas quando solicitado por uma classe:</span><span class="sxs-lookup"><span data-stu-id="435e8-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="435e8-276">A classe a seguir `Operation` implementa todas as interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="435e8-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="435e8-277">O `Operation` Construtor gera um GUID e armazena os quatro últimos caracteres na `OperationId` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="435e8-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="435e8-278">O `Startup.ConfigureServices` método cria vários registros da classe de `Operation` acordo com os tempos de vida nomeados:</span><span class="sxs-lookup"><span data-stu-id="435e8-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="435e8-279">O aplicativo de exemplo demonstra tempos de vida de objeto dentro e entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="435e8-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="435e8-280">O `IndexModel` e o middleware solicitam cada tipo de `IOperation` tipo e registram o `OperationId` para cada um:</span><span class="sxs-lookup"><span data-stu-id="435e8-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="435e8-281">Semelhante ao `IndexModel` , o middleware resolve os mesmos serviços:</span><span class="sxs-lookup"><span data-stu-id="435e8-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="435e8-282">Os serviços com escopo devem ser resolvidos no `InvokeAsync` método:</span><span class="sxs-lookup"><span data-stu-id="435e8-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="435e8-283">A saída do agente mostra:</span><span class="sxs-lookup"><span data-stu-id="435e8-283">The logger output shows:</span></span>

* <span data-ttu-id="435e8-284">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="435e8-284">*Transient* objects are always different.</span></span> <span data-ttu-id="435e8-285">O `OperationId` valor transitório é diferente no `IndexModel` e no middleware.</span><span class="sxs-lookup"><span data-stu-id="435e8-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="435e8-286">Os objetos com *escopo* são os mesmos para cada solicitação, mas diferentes em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="435e8-287">Os objetos *singleton* são os mesmos para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="435e8-288">Para reduzir a saída de log, defina "log: LogLevel: Microsoft: Error" na *appsettings.Development.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="435e8-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="435e8-289">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="435e8-289">Call services from main</span></span>

<span data-ttu-id="435e8-290">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="435e8-291">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="435e8-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="435e8-292">O exemplo a seguir mostra como acessar o serviço com escopo `IMyDependency` e chamar seu `WriteMessage` método em `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="435e8-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="435e8-293">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="435e8-293">Scope validation</span></span>

<span data-ttu-id="435e8-294">Quando o aplicativo é executado no [ambiente de desenvolvimento](xref:fundamentals/environments) e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="435e8-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="435e8-295">Os serviços com escopo não são resolvidos do provedor de serviços raiz.</span><span class="sxs-lookup"><span data-stu-id="435e8-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="435e8-296">Os serviços com escopo não são injetados em singletons.</span><span class="sxs-lookup"><span data-stu-id="435e8-296">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="435e8-297">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> é chamado.</span><span class="sxs-lookup"><span data-stu-id="435e8-297">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="435e8-298">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo quando o provedor começa com o aplicativo e é Descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="435e8-298">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="435e8-299">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="435e8-299">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="435e8-300">Se um serviço com escopo for criado no contêiner raiz, o tempo de vida do serviço será efetivamente promovido para singleton porque é descartado apenas pelo contêiner raiz quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="435e8-300">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="435e8-301">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="435e8-301">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="435e8-302">Para obter mais informações, confira [Validação de escopo](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="435e8-302">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="435e8-303">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="435e8-303">Request Services</span></span>

<span data-ttu-id="435e8-304">Os serviços disponíveis em uma solicitação de ASP.NET Core são expostos por meio da coleção [HttpContext. Requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="435e8-304">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="435e8-305">Quando os serviços são solicitados de dentro de uma solicitação, os serviços e suas dependências são resolvidos na `RequestServices` coleção.</span><span class="sxs-lookup"><span data-stu-id="435e8-305">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="435e8-306">A estrutura cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="435e8-306">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="435e8-307">Todos os serviços com escopo são válidos enquanto a solicitação está ativa.</span><span class="sxs-lookup"><span data-stu-id="435e8-307">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="435e8-308">Prefira solicitar dependências como parâmetros de construtor para resolver serviços da `RequestServices` coleção.</span><span class="sxs-lookup"><span data-stu-id="435e8-308">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="435e8-309">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="435e8-309">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="435e8-310">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="435e8-310">Design services for dependency injection</span></span>

<span data-ttu-id="435e8-311">Ao projetar serviços para injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="435e8-311">When designing services for dependency injection:</span></span>

* <span data-ttu-id="435e8-312">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="435e8-312">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="435e8-313">Evite criar o estado global criando aplicativos para usar os serviços singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-313">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="435e8-314">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-314">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="435e8-315">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="435e8-315">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="435e8-316">Torne os serviços pequenos, bem fatorados e facilmente testados.</span><span class="sxs-lookup"><span data-stu-id="435e8-316">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="435e8-317">Se uma classe tiver muitas dependências injetadas, pode ser um sinal de que a classe tem muitas responsabilidades e viola o [princípio de responsabilidade única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="435e8-317">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="435e8-318">Tente refatorar a classe movendo algumas de suas responsabilidades para novas classes.</span><span class="sxs-lookup"><span data-stu-id="435e8-318">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="435e8-319">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="435e8-319">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="435e8-320">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="435e8-320">Disposal of services</span></span>

<span data-ttu-id="435e8-321">O contêiner chama <xref:System.IDisposable.Dispose%2A> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="435e8-321">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="435e8-322">Os serviços resolvidos do contêiner nunca devem ser descartados pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="435e8-322">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="435e8-323">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-323">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="435e8-324">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="435e8-324">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="435e8-325">O console de depuração mostra a saída a seguir após cada atualização da página de índice:</span><span class="sxs-lookup"><span data-stu-id="435e8-325">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="435e8-326">Serviços não criados pelo contêiner de serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-326">Services not created by the service container</span></span>

<span data-ttu-id="435e8-327">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="435e8-327">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="435e8-328">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="435e8-328">In the preceding code:</span></span>

* <span data-ttu-id="435e8-329">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-329">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="435e8-330">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-330">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="435e8-331">O desenvolvedor é responsável por descartar os serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-331">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="435e8-332">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="435e8-332">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="435e8-333">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="435e8-333">Transient, limited lifetime</span></span>

<span data-ttu-id="435e8-334">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="435e8-334">**Scenario**</span></span>

<span data-ttu-id="435e8-335">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="435e8-335">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="435e8-336">A instância é resolvida no escopo raiz (contêiner raiz).</span><span class="sxs-lookup"><span data-stu-id="435e8-336">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="435e8-337">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="435e8-337">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="435e8-338">**Solução**</span><span class="sxs-lookup"><span data-stu-id="435e8-338">**Solution**</span></span>

<span data-ttu-id="435e8-339">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="435e8-339">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="435e8-340">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-340">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="435e8-341">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="435e8-341">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="435e8-342">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="435e8-342">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="435e8-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="435e8-344">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="435e8-344">Shared instance, limited lifetime</span></span>

<span data-ttu-id="435e8-345">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="435e8-345">**Scenario**</span></span>

<span data-ttu-id="435e8-346">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada em vários serviços, mas a <xref:System.IDisposable> instância deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="435e8-346">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="435e8-347">**Solução**</span><span class="sxs-lookup"><span data-stu-id="435e8-347">**Solution**</span></span>

<span data-ttu-id="435e8-348">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="435e8-348">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="435e8-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="435e8-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="435e8-350">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="435e8-350">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="435e8-351">Descarte o escopo quando ele não for mais necessário.</span><span class="sxs-lookup"><span data-stu-id="435e8-351">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="435e8-352">Diretrizes gerais de IDisposable</span><span class="sxs-lookup"><span data-stu-id="435e8-352">General IDisposable guidelines</span></span>

* <span data-ttu-id="435e8-353">Não registre <xref:System.IDisposable> instâncias com um tempo de vida transitório.</span><span class="sxs-lookup"><span data-stu-id="435e8-353">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="435e8-354">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="435e8-354">Use the factory pattern instead.</span></span>
* <span data-ttu-id="435e8-355">Não resolva <xref:System.IDisposable> instâncias com um tempo de vida transitório ou com escopo definido no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="435e8-355">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="435e8-356">A única exceção a isso é se o aplicativo cria/recria e descarta <xref:System.IServiceProvider> , mas esse não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="435e8-356">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="435e8-357">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="435e8-357">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="435e8-358">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-358">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="435e8-359">Use escopos para controlar os tempos de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-359">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="435e8-360">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="435e8-360">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="435e8-361">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="435e8-361">Default service container replacement</span></span>

<span data-ttu-id="435e8-362">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="435e8-362">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="435e8-363">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual ele não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="435e8-363">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="435e8-364">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="435e8-364">Property injection</span></span>
* <span data-ttu-id="435e8-365">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="435e8-365">Injection based on name</span></span>
* <span data-ttu-id="435e8-366">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="435e8-366">Child containers</span></span>
* <span data-ttu-id="435e8-367">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="435e8-367">Custom lifetime management</span></span>
* <span data-ttu-id="435e8-368">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="435e8-368">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="435e8-369">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="435e8-369">Convention-based registration</span></span>

<span data-ttu-id="435e8-370">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="435e8-370">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="435e8-371">Autofac</span><span class="sxs-lookup"><span data-stu-id="435e8-371">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="435e8-372">DryIoc</span><span class="sxs-lookup"><span data-stu-id="435e8-372">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="435e8-373">Grace</span><span class="sxs-lookup"><span data-stu-id="435e8-373">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="435e8-374">LightInject</span><span class="sxs-lookup"><span data-stu-id="435e8-374">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="435e8-375">Lamar</span><span class="sxs-lookup"><span data-stu-id="435e8-375">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="435e8-376">Stashbox</span><span class="sxs-lookup"><span data-stu-id="435e8-376">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="435e8-377">Unity</span><span class="sxs-lookup"><span data-stu-id="435e8-377">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="435e8-378">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="435e8-378">Thread safety</span></span>

<span data-ttu-id="435e8-379">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="435e8-379">Create thread-safe singleton services.</span></span> <span data-ttu-id="435e8-380">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também poderá exigir a segurança do thread dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-380">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="435e8-381">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="435e8-381">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="435e8-382">Como um construtor de tipo ( `static` ), é garantido que ele seja chamado apenas uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="435e8-382">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="435e8-383">Recomendações</span><span class="sxs-lookup"><span data-stu-id="435e8-383">Recommendations</span></span>

* <span data-ttu-id="435e8-384">`async/await` e a `Task` resolução de serviço baseada não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="435e8-384">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="435e8-385">Como o C# não dá suporte a construtores assíncronos, use métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-385">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="435e8-386">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-386">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="435e8-387">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-387">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="435e8-388">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="435e8-388">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="435e8-389">Da mesma forma, evite objetos de "portador de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="435e8-389">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="435e8-390">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="435e8-390">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="435e8-391">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-391">Avoid static access to services.</span></span> <span data-ttu-id="435e8-392">Por exemplo, evite capturar [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) como um campo ou propriedade estática para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="435e8-392">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="435e8-393">Mantenha as fábricas de injeção rápida e síncrona.</span><span class="sxs-lookup"><span data-stu-id="435e8-393">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="435e8-394">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="435e8-394">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="435e8-395">Por exemplo, não invoque <xref:System.IServiceProvider.GetService%2A> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="435e8-395">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="435e8-396">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="435e8-396">**Incorrect:**</span></span>

    ![Código incorreto](dependency-injection/_static/bad.png)

  <span data-ttu-id="435e8-398">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="435e8-398">**Correct**:</span></span>

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
* <span data-ttu-id="435e8-399">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="435e8-399">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="435e8-400">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="435e8-400">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="435e8-401">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="435e8-401">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="435e8-402">Evite chamadas para <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="435e8-402">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="435e8-403">Chamar `BuildServiceProvider` normalmente acontece quando o desenvolvedor deseja resolver um serviço no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="435e8-403">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="435e8-404">Por exemplo, considere o caso em que o `LoginPath` é carregado a partir da configuração.</span><span class="sxs-lookup"><span data-stu-id="435e8-404">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="435e8-405">Evite a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="435e8-405">Avoid the following approach:</span></span>

  ![código inadequado chamando BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="435e8-407">Na imagem anterior, selecionar a linha ondulada verde em `services.BuildServiceProvider` mostra o seguinte aviso de ASP0000:</span><span class="sxs-lookup"><span data-stu-id="435e8-407">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="435e8-408">ASP0000 chamar ' BuildServiceProvider ' do código do aplicativo resulta em uma cópia adicional dos serviços singleton que estão sendo criados.</span><span class="sxs-lookup"><span data-stu-id="435e8-408">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="435e8-409">Considere alternativas como a injeção de dependência de serviços como parâmetros para ' Configurar '.</span><span class="sxs-lookup"><span data-stu-id="435e8-409">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="435e8-410">`BuildServiceProvider`A chamada cria um segundo contêiner, que pode criar singletons rasgados e causar referências a grafos de objeto em vários contêineres.</span><span class="sxs-lookup"><span data-stu-id="435e8-410">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="435e8-411">Uma maneira correta de `LoginPath` se obter é usar o suporte interno do padrão de opções para di:</span><span class="sxs-lookup"><span data-stu-id="435e8-411">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="435e8-412">Serviços inposados transitórios são capturados pelo contêiner para descarte.</span><span class="sxs-lookup"><span data-stu-id="435e8-412">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="435e8-413">Isso pode transformar em um vazamento de memória se resolvido no contêiner de nível superior.</span><span class="sxs-lookup"><span data-stu-id="435e8-413">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="435e8-414">Habilite a validação de escopo para certificar-se de que o aplicativo não tem singletons que capturam serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="435e8-414">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="435e8-415">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="435e8-415">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="435e8-416">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="435e8-416">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="435e8-417">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-417">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="435e8-418">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="435e8-418">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="435e8-419">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="435e8-419">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="435e8-420">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="435e8-420">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="435e8-421">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) é uma estrutura de aplicativo para a criação de aplicativos modulares multilocatários em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="435e8-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="435e8-422">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="435e8-422">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="435e8-423">Consulte os [exemplos do Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obter exemplos de como criar aplicativos modulares e multilocatários usando apenas a estrutura do Orchard Core sem qualquer um de seus recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="435e8-423">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="435e8-424">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="435e8-424">Framework-provided services</span></span>

<span data-ttu-id="435e8-425">O `Startup.ConfigureServices` método registra os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="435e8-425">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="435e8-426">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="435e8-426">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="435e8-427">Para aplicativos baseados nos modelos de ASP.NET Core, a estrutura registra mais de 250 serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-427">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="435e8-428">A tabela a seguir lista uma pequena amostra desses serviços registrados no Framework:</span><span class="sxs-lookup"><span data-stu-id="435e8-428">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="435e8-429">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-429">Service Type</span></span>                                                                                    | <span data-ttu-id="435e8-430">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="435e8-430">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="435e8-431">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-431">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="435e8-432">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-432">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="435e8-433">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="435e8-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="435e8-435">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-435">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="435e8-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="435e8-437">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-437">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="435e8-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-438">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="435e8-439">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="435e8-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="435e8-441">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="435e8-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-442">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="435e8-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="435e8-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-444">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="435e8-445">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="435e8-445">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="435e8-446">Padrões de conferência NDC para desenvolvimento de aplicativo de DI</span><span class="sxs-lookup"><span data-stu-id="435e8-446">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="435e8-447">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="435e8-447">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="435e8-448">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="435e8-448">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="435e8-449">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="435e8-449">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="435e8-450">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="435e8-450">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="435e8-451">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="435e8-451">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="435e8-452">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="435e8-452">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="435e8-453">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-453">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="435e8-454">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="435e8-454">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="435e8-455">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="435e8-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="435e8-456">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="435e8-456">Overview of dependency injection</span></span>

<span data-ttu-id="435e8-457">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="435e8-457">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="435e8-458">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="435e8-458">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="435e8-459">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-459">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="435e8-460">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="435e8-460">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="435e8-461">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="435e8-461">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="435e8-462">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="435e8-462">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="435e8-463">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="435e8-463">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="435e8-464">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-464">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="435e8-465">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-465">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="435e8-466">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="435e8-466">This implementation is difficult to unit test.</span></span> <span data-ttu-id="435e8-467">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="435e8-467">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="435e8-468">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="435e8-468">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="435e8-469">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-469">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="435e8-470">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-470">Registration of the dependency in a service container.</span></span> <span data-ttu-id="435e8-471">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="435e8-471">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="435e8-472">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-472">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="435e8-473">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="435e8-473">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="435e8-474">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="435e8-474">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="435e8-475">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="435e8-475">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="435e8-476">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="435e8-476">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="435e8-477">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="435e8-477">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="435e8-478">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="435e8-478">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="435e8-479">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-479">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="435e8-480">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="435e8-480">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="435e8-481">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="435e8-481">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="435e8-482">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-482">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="435e8-483">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="435e8-483">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="435e8-484">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-484">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="435e8-485">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="435e8-485">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="435e8-486">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="435e8-486">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="435e8-487">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-487">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="435e8-488">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="435e8-488">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="435e8-489">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-489">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="435e8-490">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="435e8-490">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="435e8-491">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="435e8-491">We recommended that apps follow this convention.</span></span> <span data-ttu-id="435e8-492">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-492">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="435e8-493">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="435e8-493">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="435e8-494">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="435e8-494">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="435e8-495">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-495">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="435e8-496">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="435e8-496">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="435e8-497">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="435e8-497">Services injected into Startup</span></span>

<span data-ttu-id="435e8-498">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="435e8-498">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="435e8-499">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="435e8-499">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="435e8-500">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="435e8-500">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="435e8-501">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="435e8-501">Framework-provided services</span></span>

<span data-ttu-id="435e8-502">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="435e8-502">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="435e8-503">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="435e8-503">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="435e8-504">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-504">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="435e8-505">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="435e8-505">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="435e8-506">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-506">Service Type</span></span> | <span data-ttu-id="435e8-507">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="435e8-507">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="435e8-508">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-508">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="435e8-509">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-509">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="435e8-510">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="435e8-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="435e8-512">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="435e8-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="435e8-514">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-514">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="435e8-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-515">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="435e8-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="435e8-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="435e8-518">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="435e8-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-519">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="435e8-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="435e8-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-521">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="435e8-522">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="435e8-522">Register additional services with extension methods</span></span>

<span data-ttu-id="435e8-523">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-523">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="435e8-524">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="435e8-524">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="435e8-525">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="435e8-525">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="435e8-526">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-526">Service lifetimes</span></span>

<span data-ttu-id="435e8-527">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="435e8-527">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="435e8-528">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="435e8-528">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="435e8-529">Transitório</span><span class="sxs-lookup"><span data-stu-id="435e8-529">Transient</span></span>

<span data-ttu-id="435e8-530">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-530">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="435e8-531">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="435e8-531">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="435e8-532">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-532">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="435e8-533">Com escopo</span><span class="sxs-lookup"><span data-stu-id="435e8-533">Scoped</span></span>

<span data-ttu-id="435e8-534">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="435e8-534">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="435e8-535">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="435e8-535">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="435e8-536">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="435e8-536">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="435e8-537">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-537">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="435e8-538">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="435e8-538">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="435e8-539">Singleton</span><span class="sxs-lookup"><span data-stu-id="435e8-539">Singleton</span></span>

<span data-ttu-id="435e8-540">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="435e8-540">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="435e8-541">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="435e8-541">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="435e8-542">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-542">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="435e8-543">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-543">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="435e8-544">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-544">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="435e8-545">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-545">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="435e8-546">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="435e8-546">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="435e8-547">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="435e8-547">Service registration methods</span></span>

<span data-ttu-id="435e8-548">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="435e8-548">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="435e8-549">Método</span><span class="sxs-lookup"><span data-stu-id="435e8-549">Method</span></span> | <span data-ttu-id="435e8-550">Automática</span><span class="sxs-lookup"><span data-stu-id="435e8-550">Automatic</span></span><br><span data-ttu-id="435e8-551">objeto</span><span class="sxs-lookup"><span data-stu-id="435e8-551">object</span></span><br><span data-ttu-id="435e8-552">descarte</span><span class="sxs-lookup"><span data-stu-id="435e8-552">disposal</span></span> | <span data-ttu-id="435e8-553">Vários</span><span class="sxs-lookup"><span data-stu-id="435e8-553">Multiple</span></span><br><span data-ttu-id="435e8-554">implementações</span><span class="sxs-lookup"><span data-stu-id="435e8-554">implementations</span></span> | <span data-ttu-id="435e8-555">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="435e8-555">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="435e8-556">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="435e8-556">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="435e8-557">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-557">Yes</span></span> | <span data-ttu-id="435e8-558">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-558">Yes</span></span> | <span data-ttu-id="435e8-559">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-559">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="435e8-560">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="435e8-560">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="435e8-561">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-561">Yes</span></span> | <span data-ttu-id="435e8-562">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-562">Yes</span></span> | <span data-ttu-id="435e8-563">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-563">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="435e8-564">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="435e8-564">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="435e8-565">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-565">Yes</span></span> | <span data-ttu-id="435e8-566">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-566">No</span></span> | <span data-ttu-id="435e8-567">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-567">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="435e8-568">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="435e8-568">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="435e8-569">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-569">No</span></span> | <span data-ttu-id="435e8-570">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-570">Yes</span></span> | <span data-ttu-id="435e8-571">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-571">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="435e8-572">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="435e8-572">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="435e8-573">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-573">No</span></span> | <span data-ttu-id="435e8-574">Não</span><span class="sxs-lookup"><span data-stu-id="435e8-574">No</span></span> | <span data-ttu-id="435e8-575">Sim</span><span class="sxs-lookup"><span data-stu-id="435e8-575">Yes</span></span> |

<span data-ttu-id="435e8-576">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="435e8-576">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="435e8-577">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="435e8-577">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="435e8-578">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="435e8-578">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="435e8-579">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="435e8-579">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="435e8-580">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="435e8-580">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="435e8-581">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="435e8-581">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="435e8-582">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="435e8-582">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="435e8-583">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="435e8-583">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="435e8-584">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="435e8-584">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="435e8-585">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="435e8-585">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="435e8-586">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="435e8-586">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="435e8-587">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="435e8-587">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="435e8-588">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="435e8-588">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="435e8-589">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="435e8-589">Constructor injection behavior</span></span>

<span data-ttu-id="435e8-590">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="435e8-590">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="435e8-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="435e8-592">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="435e8-592">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="435e8-593">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="435e8-593">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="435e8-594">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="435e8-594">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="435e8-595">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="435e8-595">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="435e8-596">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-596">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="435e8-597">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="435e8-597">Entity Framework contexts</span></span>

<span data-ttu-id="435e8-598">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="435e8-598">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="435e8-599">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="435e8-599">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="435e8-600">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-600">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="435e8-601">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="435e8-601">Lifetime and registration options</span></span>

<span data-ttu-id="435e8-602">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="435e8-602">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="435e8-603">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="435e8-603">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="435e8-604">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="435e8-604">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="435e8-605">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="435e8-605">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="435e8-606">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="435e8-606">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="435e8-607">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="435e8-607">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="435e8-608">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="435e8-608">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="435e8-609">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="435e8-609">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="435e8-610">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="435e8-610">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="435e8-611">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="435e8-611">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="435e8-612">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="435e8-612">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="435e8-613">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-613">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="435e8-614">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="435e8-614">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="435e8-615">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="435e8-615">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="435e8-616">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="435e8-616">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="435e8-617">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="435e8-617">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="435e8-618">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="435e8-618">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="435e8-619">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="435e8-619">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="435e8-620">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="435e8-620">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="435e8-621">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="435e8-621">**First request:**</span></span>

<span data-ttu-id="435e8-622">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="435e8-622">Controller operations:</span></span>

<span data-ttu-id="435e8-623">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="435e8-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="435e8-624">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="435e8-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="435e8-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="435e8-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="435e8-626">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="435e8-626">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="435e8-627">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="435e8-627">`OperationService` operations:</span></span>

<span data-ttu-id="435e8-628">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="435e8-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="435e8-629">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="435e8-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="435e8-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="435e8-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="435e8-631">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="435e8-631">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="435e8-632">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="435e8-632">**Second request:**</span></span>

<span data-ttu-id="435e8-633">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="435e8-633">Controller operations:</span></span>

<span data-ttu-id="435e8-634">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="435e8-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="435e8-635">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="435e8-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="435e8-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="435e8-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="435e8-637">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="435e8-637">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="435e8-638">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="435e8-638">`OperationService` operations:</span></span>

<span data-ttu-id="435e8-639">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="435e8-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="435e8-640">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="435e8-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="435e8-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="435e8-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="435e8-642">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="435e8-642">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="435e8-643">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="435e8-643">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="435e8-644">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="435e8-644">*Transient* objects are always different.</span></span> <span data-ttu-id="435e8-645">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="435e8-645">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="435e8-646">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="435e8-646">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="435e8-647">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="435e8-647">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="435e8-648">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="435e8-648">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="435e8-649">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="435e8-649">Call services from main</span></span>

<span data-ttu-id="435e8-650">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-650">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="435e8-651">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="435e8-651">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="435e8-652">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="435e8-652">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="435e8-653">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="435e8-653">Scope validation</span></span>

<span data-ttu-id="435e8-654">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="435e8-654">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="435e8-655">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="435e8-655">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="435e8-656">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="435e8-656">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="435e8-657">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="435e8-657">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="435e8-658">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="435e8-658">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="435e8-659">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="435e8-659">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="435e8-660">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="435e8-660">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="435e8-661">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="435e8-661">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="435e8-662">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="435e8-662">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="435e8-663">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="435e8-663">Request Services</span></span>

<span data-ttu-id="435e8-664">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="435e8-664">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="435e8-665">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="435e8-665">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="435e8-666">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="435e8-666">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="435e8-667">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-667">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="435e8-668">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-668">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="435e8-669">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="435e8-669">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="435e8-670">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="435e8-670">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="435e8-671">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="435e8-671">Design services for dependency injection</span></span>

<span data-ttu-id="435e8-672">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="435e8-672">Best practices are to:</span></span>

* <span data-ttu-id="435e8-673">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-673">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="435e8-674">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="435e8-674">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="435e8-675">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="435e8-675">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="435e8-676">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-676">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="435e8-677">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="435e8-677">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="435e8-678">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="435e8-678">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="435e8-679">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="435e8-679">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="435e8-680">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="435e8-680">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="435e8-681">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="435e8-681">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="435e8-682">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="435e8-682">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="435e8-683">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="435e8-683">Disposal of services</span></span>

<span data-ttu-id="435e8-684">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="435e8-684">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="435e8-685">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-685">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="435e8-686">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="435e8-686">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="435e8-687">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="435e8-687">In the following example:</span></span>

* <span data-ttu-id="435e8-688">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-688">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="435e8-689">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-689">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="435e8-690">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-690">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="435e8-691">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="435e8-691">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="435e8-692">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="435e8-692">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="435e8-693">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="435e8-693">Transient, limited lifetime</span></span>

<span data-ttu-id="435e8-694">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="435e8-694">**Scenario**</span></span>

<span data-ttu-id="435e8-695">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="435e8-695">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="435e8-696">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="435e8-696">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="435e8-697">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="435e8-697">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="435e8-698">**Solução**</span><span class="sxs-lookup"><span data-stu-id="435e8-698">**Solution**</span></span>

<span data-ttu-id="435e8-699">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="435e8-699">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="435e8-700">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="435e8-700">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="435e8-701">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="435e8-701">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="435e8-702">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="435e8-702">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="435e8-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="435e8-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="435e8-704">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="435e8-704">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="435e8-705">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="435e8-705">**Scenario**</span></span>

<span data-ttu-id="435e8-706">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="435e8-706">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="435e8-707">**Solução**</span><span class="sxs-lookup"><span data-stu-id="435e8-707">**Solution**</span></span>

<span data-ttu-id="435e8-708">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="435e8-708">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="435e8-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="435e8-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="435e8-710">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="435e8-710">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="435e8-711">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="435e8-711">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="435e8-712">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="435e8-712">General Guidelines</span></span>

* <span data-ttu-id="435e8-713">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="435e8-713">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="435e8-714">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="435e8-714">Use the factory pattern instead.</span></span>
* <span data-ttu-id="435e8-715">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="435e8-715">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="435e8-716">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="435e8-716">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="435e8-717">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="435e8-717">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="435e8-718">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="435e8-718">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="435e8-719">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-719">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="435e8-720">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="435e8-720">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="435e8-721">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="435e8-721">Default service container replacement</span></span>

<span data-ttu-id="435e8-722">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="435e8-722">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="435e8-723">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="435e8-723">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="435e8-724">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="435e8-724">Property injection</span></span>
* <span data-ttu-id="435e8-725">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="435e8-725">Injection based on name</span></span>
* <span data-ttu-id="435e8-726">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="435e8-726">Child containers</span></span>
* <span data-ttu-id="435e8-727">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="435e8-727">Custom lifetime management</span></span>
* <span data-ttu-id="435e8-728">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="435e8-728">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="435e8-729">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="435e8-729">Convention-based registration</span></span>

<span data-ttu-id="435e8-730">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="435e8-730">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="435e8-731">Autofac</span><span class="sxs-lookup"><span data-stu-id="435e8-731">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="435e8-732">DryIoc</span><span class="sxs-lookup"><span data-stu-id="435e8-732">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="435e8-733">Grace</span><span class="sxs-lookup"><span data-stu-id="435e8-733">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="435e8-734">LightInject</span><span class="sxs-lookup"><span data-stu-id="435e8-734">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="435e8-735">Lamar</span><span class="sxs-lookup"><span data-stu-id="435e8-735">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="435e8-736">Stashbox</span><span class="sxs-lookup"><span data-stu-id="435e8-736">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="435e8-737">Unity</span><span class="sxs-lookup"><span data-stu-id="435e8-737">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="435e8-738">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="435e8-738">Thread safety</span></span>

<span data-ttu-id="435e8-739">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="435e8-739">Create thread-safe singleton services.</span></span> <span data-ttu-id="435e8-740">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="435e8-740">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="435e8-741">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="435e8-741">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="435e8-742">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="435e8-742">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="435e8-743">Recomendações</span><span class="sxs-lookup"><span data-stu-id="435e8-743">Recommendations</span></span>

* <span data-ttu-id="435e8-744">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="435e8-744">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="435e8-745">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-745">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="435e8-746">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-746">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="435e8-747">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="435e8-747">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="435e8-748">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="435e8-748">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="435e8-749">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="435e8-749">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="435e8-750">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="435e8-750">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="435e8-751">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="435e8-751">Avoid static access to services.</span></span> <span data-ttu-id="435e8-752">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="435e8-752">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="435e8-753">Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="435e8-753">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="435e8-754">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="435e8-754">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="435e8-755">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="435e8-755">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="435e8-756">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="435e8-756">**Correct**:</span></span>

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

* <span data-ttu-id="435e8-757">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="435e8-757">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="435e8-758">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="435e8-758">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="435e8-759">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="435e8-759">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="435e8-760">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="435e8-760">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="435e8-761">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="435e8-761">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="435e8-762">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="435e8-762">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="435e8-763">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="435e8-763">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="435e8-764">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="435e8-764">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="435e8-765">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="435e8-765">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="435e8-766">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="435e8-766">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="435e8-767">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="435e8-767">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="435e8-768">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="435e8-768">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
