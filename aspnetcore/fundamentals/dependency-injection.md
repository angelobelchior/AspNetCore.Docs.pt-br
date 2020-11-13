---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 9f9f41f4d25867c43cd49640bc76ef63f9415eb2
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570192"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d35aa-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d35aa-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d35aa-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d35aa-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d35aa-105">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d35aa-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d35aa-107">Para obter informações sobre como usar a injeção de dependência em aplicativos diferentes de aplicativos Web, consulte [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d35aa-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="d35aa-108">Para obter mais informações sobre injeção de dependência de opções, consulte <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="d35aa-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d35aa-109">Este tópico fornece informações sobre injeção de dependência no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d35aa-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="d35aa-110">A documentação principal sobre como usar a injeção de dependência está contida na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d35aa-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="d35aa-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d35aa-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d35aa-112">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d35aa-112">Overview of dependency injection</span></span>

<span data-ttu-id="d35aa-113">Uma *dependência* é um objeto do qual outro objeto depende.</span><span class="sxs-lookup"><span data-stu-id="d35aa-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="d35aa-114">Examine a seguinte `MyDependency` classe com um `WriteMessage` método de que outras classes dependem:</span><span class="sxs-lookup"><span data-stu-id="d35aa-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="d35aa-115">Uma classe pode criar uma instância da `MyDependency` classe para fazer uso de seu `WriteMessage` método.</span><span class="sxs-lookup"><span data-stu-id="d35aa-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="d35aa-116">No exemplo a seguir, a `MyDependency` classe é uma dependência da `IndexModel` classe:</span><span class="sxs-lookup"><span data-stu-id="d35aa-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d35aa-117">A classe cria e depende diretamente da `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="d35aa-118">As dependências de código, como no exemplo anterior, são problemáticas e devem ser evitadas pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="d35aa-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d35aa-119">Para substituir `MyDependency` por uma implementação diferente, a `IndexModel` classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="d35aa-120">Se `MyDependency` tiver dependências, elas também deverão ser configuradas pela `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="d35aa-121">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d35aa-122">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d35aa-123">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d35aa-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d35aa-124">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d35aa-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d35aa-125">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="d35aa-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d35aa-126">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d35aa-127">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d35aa-128">Normalmente, os serviços são registrados no método do aplicativo `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d35aa-129">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d35aa-130">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="d35aa-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d35aa-131">No [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a `IMyDependency` interface define o `WriteMessage` método:</span><span class="sxs-lookup"><span data-stu-id="d35aa-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d35aa-132">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d35aa-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d35aa-133">O aplicativo de exemplo registra o `IMyDependency` serviço com o tipo concreto `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d35aa-134">O <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> método registra o serviço com um tempo de vida de escopo, o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="d35aa-135">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d35aa-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="d35aa-136">No aplicativo de exemplo, o `IMyDependency` serviço é solicitado e usado para chamar o `WriteMessage` método:</span><span class="sxs-lookup"><span data-stu-id="d35aa-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="d35aa-137">Usando o padrão DI, o controlador:</span><span class="sxs-lookup"><span data-stu-id="d35aa-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="d35aa-138">Não usa o tipo concreto `MyDependency` , apenas a `IMyDependency` interface que ele implementa.</span><span class="sxs-lookup"><span data-stu-id="d35aa-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="d35aa-139">Isso facilita a alteração da implementação que o controlador usa sem modificar o controlador.</span><span class="sxs-lookup"><span data-stu-id="d35aa-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="d35aa-140">Não cria uma instância do `MyDependency` , ela é criada pelo contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="d35aa-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="d35aa-141">A implementação da `IMyDependency` interface pode ser aprimorada usando a API de registro em log interna:</span><span class="sxs-lookup"><span data-stu-id="d35aa-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="d35aa-142">O `ConfigureServices` método atualizado registra a nova `IMyDependency` implementação:</span><span class="sxs-lookup"><span data-stu-id="d35aa-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="d35aa-143">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601> , que ele solicita no construtor.</span><span class="sxs-lookup"><span data-stu-id="d35aa-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="d35aa-144">`ILogger<TCategoryName>` é um [serviço fornecido pelo Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="d35aa-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="d35aa-145">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d35aa-146">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d35aa-147">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="d35aa-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d35aa-148">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência* , *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="d35aa-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="d35aa-149">O contêiner resolve aproveitando `ILogger<TCategoryName>` os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar cada [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="d35aa-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="d35aa-150">Na terminologia de injeção de dependência, um serviço:</span><span class="sxs-lookup"><span data-stu-id="d35aa-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="d35aa-151">Normalmente é um objeto que fornece um serviço para outros objetos, como o `IMyDependency` serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="d35aa-152">O não está relacionado a um serviço Web, embora o serviço possa usar um serviço Web.</span><span class="sxs-lookup"><span data-stu-id="d35aa-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="d35aa-153">A estrutura fornece um sistema de [registro em log](xref:fundamentals/logging/index) robusto.</span><span class="sxs-lookup"><span data-stu-id="d35aa-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="d35aa-154">As `IMyDependency` implementações mostradas nos exemplos anteriores foram escritas para demonstrar a di básica, não para implementar o registro em log.</span><span class="sxs-lookup"><span data-stu-id="d35aa-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="d35aa-155">A maioria dos aplicativos não deve precisar gravar agentes.</span><span class="sxs-lookup"><span data-stu-id="d35aa-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="d35aa-156">O código a seguir demonstra como usar o log padrão, que não exige que nenhum serviço seja registrado no `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d35aa-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="d35aa-157">Usando o código anterior, não há necessidade de atualizar `ConfigureServices` , porque o [registro em log](xref:fundamentals/logging/index) é fornecido pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="d35aa-158">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="d35aa-158">Services injected into Startup</span></span>

<span data-ttu-id="d35aa-159">Os serviços podem ser injetados no `Startup` Construtor e no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="d35aa-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="d35aa-160">Somente os seguintes serviços podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="d35aa-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d35aa-161">Qualquer serviço registrado com o contêiner DI pode ser injetado no `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="d35aa-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="d35aa-162">Para obter mais informações, consulte <xref:fundamentals/startup> e [acessar a configuração na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d35aa-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="d35aa-163">Registrar grupos de serviços com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="d35aa-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="d35aa-164">O ASP.NET Core Framework usa uma Convenção para registrar um grupo de serviços relacionados.</span><span class="sxs-lookup"><span data-stu-id="d35aa-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="d35aa-165">A Convenção é usar um método de `Add{GROUP_NAME}` extensão único para registrar todos os serviços exigidos por um recurso de estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="d35aa-166">Por exemplo, o <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> método de extensão registra os serviços necessários para controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="d35aa-166">For example, the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="d35aa-167">O código a seguir é gerado pelo :::no-loc(Razor)::: modelo de páginas usando contas de usuário individuais e mostra como adicionar serviços adicionais ao contêiner usando os métodos de extensão <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> :</span><span class="sxs-lookup"><span data-stu-id="d35aa-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="d35aa-168">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-168">Service lifetimes</span></span>

<span data-ttu-id="d35aa-169">Confira [tempos de vida de serviço](/dotnet/core/extensions/dependency-injection#service-lifetimes) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="d35aa-170">Para usar serviços com escopo no middleware, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d35aa-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="d35aa-171">Injetar o serviço no `Invoke` método ou do middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d35aa-172">O uso de [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) gera uma exceção de tempo de execução porque força o serviço de escopo a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="d35aa-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="d35aa-173">O exemplo na seção de [Opções de tempo de vida e de registro](#lifetime-and-registration-options) demonstra a `InvokeAsync` abordagem.</span><span class="sxs-lookup"><span data-stu-id="d35aa-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="d35aa-174">Use o [middleware baseado em fábrica](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="d35aa-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="d35aa-175">O middleware registrado usando essa abordagem é ativado por solicitação do cliente (conexão), que permite que os serviços com escopo sejam injetados no método do middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="d35aa-176">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d35aa-177">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-177">Service registration methods</span></span>

<span data-ttu-id="d35aa-178">Consulte [métodos de registro de serviço](/dotnet/core/extensions/dependency-injection#service-registration-methods) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="d35aa-179">É comum usar várias implementações ao [simular tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d35aa-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d35aa-180">O registro de um serviço com apenas um tipo de implementação é equivalente ao registro desse serviço com a mesma implementação e tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="d35aa-181">É por isso que várias implementações de um serviço não podem ser registradas usando os métodos que não usam um tipo de serviço explícito.</span><span class="sxs-lookup"><span data-stu-id="d35aa-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="d35aa-182">Esses métodos podem registrar várias *instâncias* de um serviço, mas todos terão o mesmo tipo de *implementação* .</span><span class="sxs-lookup"><span data-stu-id="d35aa-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="d35aa-183">Qualquer um dos métodos de registro de serviço acima pode ser usado para registrar várias instâncias de serviço do mesmo tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="d35aa-184">No exemplo a seguir, `AddSingleton` é chamado duas vezes com `IMyDependency` como o tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="d35aa-185">A segunda chamada para `AddSingleton` substitui a anterior quando resolvida como `IMyDependency` e a adiciona à anterior quando vários serviços são resolvidos por meio de `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="d35aa-186">Os serviços aparecem na ordem em que foram registrados quando resolvidos por meio de `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="d35aa-187">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="d35aa-187">Constructor injection behavior</span></span>

<span data-ttu-id="d35aa-188">Consulte [comportamento de injeção de Construtor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d35aa-189">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d35aa-189">Entity Framework contexts</span></span>

<span data-ttu-id="d35aa-190">Por padrão, os contextos de Entity Framework são adicionados ao contêiner de serviço usando o [tempo de vida do escopo](#service-lifetimes) porque as operações de banco de dados do aplicativo Web normalmente são delimitadas à solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d35aa-191">Para usar um tempo de vida diferente, especifique o tempo de vida usando uma <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="d35aa-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="d35aa-192">Os serviços de um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida menor do que o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d35aa-193">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="d35aa-193">Lifetime and registration options</span></span>

<span data-ttu-id="d35aa-194">Para demonstrar a diferença entre tempos de vida de serviço e suas opções de registro, considere as seguintes interfaces que representam uma tarefa como uma operação com um identificador, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="d35aa-195">Dependendo de como o tempo de vida do serviço de uma operação está configurado para as seguintes interfaces, o contêiner fornecerá as mesmas instâncias do serviço ou as mesmas quando solicitado por uma classe:</span><span class="sxs-lookup"><span data-stu-id="d35aa-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d35aa-196">A classe a seguir `Operation` implementa todas as interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="d35aa-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="d35aa-197">O `Operation` Construtor gera um GUID e armazena os quatro últimos caracteres na `OperationId` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d35aa-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d35aa-198">O `Startup.ConfigureServices` método cria vários registros da classe de `Operation` acordo com os tempos de vida nomeados:</span><span class="sxs-lookup"><span data-stu-id="d35aa-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d35aa-199">O aplicativo de exemplo demonstra tempos de vida de objeto dentro e entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="d35aa-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="d35aa-200">O `IndexModel` e o middleware solicitam cada tipo de `IOperation` tipo e registram o `OperationId` para cada um:</span><span class="sxs-lookup"><span data-stu-id="d35aa-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d35aa-201">Semelhante ao `IndexModel` , o middleware resolve os mesmos serviços:</span><span class="sxs-lookup"><span data-stu-id="d35aa-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="d35aa-202">Os serviços com escopo devem ser resolvidos no `InvokeAsync` método:</span><span class="sxs-lookup"><span data-stu-id="d35aa-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="d35aa-203">A saída do agente mostra:</span><span class="sxs-lookup"><span data-stu-id="d35aa-203">The logger output shows:</span></span>

* <span data-ttu-id="d35aa-204">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="d35aa-204">*Transient* objects are always different.</span></span> <span data-ttu-id="d35aa-205">O `OperationId` valor transitório é diferente no `IndexModel` e no middleware.</span><span class="sxs-lookup"><span data-stu-id="d35aa-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="d35aa-206">Os objetos com *escopo* são os mesmos para cada solicitação, mas diferentes em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="d35aa-207">Os objetos *singleton* são os mesmos para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="d35aa-208">Para reduzir a saída de log, defina "log: LogLevel: Microsoft: Error" na *appsettings.Development.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="d35aa-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="d35aa-209">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="d35aa-209">Call services from main</span></span>

<span data-ttu-id="d35aa-210">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d35aa-211">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d35aa-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="d35aa-212">O exemplo a seguir mostra como acessar o serviço com escopo `IMyDependency` e chamar seu `WriteMessage` método em `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d35aa-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="d35aa-213">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="d35aa-213">Scope validation</span></span>

<span data-ttu-id="d35aa-214">Consulte [comportamento de injeção de Construtor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="d35aa-215">Para obter mais informações, confira [Validação de escopo](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d35aa-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="d35aa-216">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="d35aa-216">Request Services</span></span>

<span data-ttu-id="d35aa-217">Os serviços disponíveis em uma solicitação de ASP.NET Core são expostos por meio da coleção [HttpContext. Requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="d35aa-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="d35aa-218">Quando os serviços são solicitados de dentro de uma solicitação, os serviços e suas dependências são resolvidos na `RequestServices` coleção.</span><span class="sxs-lookup"><span data-stu-id="d35aa-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="d35aa-219">A estrutura cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="d35aa-220">Todos os serviços com escopo são válidos enquanto a solicitação está ativa.</span><span class="sxs-lookup"><span data-stu-id="d35aa-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="d35aa-221">Prefira solicitar dependências como parâmetros de construtor para resolver serviços da `RequestServices` coleção.</span><span class="sxs-lookup"><span data-stu-id="d35aa-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="d35aa-222">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="d35aa-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d35aa-223">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d35aa-223">Design services for dependency injection</span></span>

<span data-ttu-id="d35aa-224">Ao projetar serviços para injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="d35aa-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="d35aa-225">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="d35aa-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d35aa-226">Evite criar o estado global criando aplicativos para usar os serviços singleton.</span><span class="sxs-lookup"><span data-stu-id="d35aa-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="d35aa-227">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d35aa-228">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="d35aa-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d35aa-229">Torne os serviços pequenos, bem fatorados e facilmente testados.</span><span class="sxs-lookup"><span data-stu-id="d35aa-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d35aa-230">Se uma classe tiver muitas dependências injetadas, pode ser um sinal de que a classe tem muitas responsabilidades e viola o [princípio de responsabilidade única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d35aa-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d35aa-231">Tente refatorar a classe movendo algumas de suas responsabilidades para novas classes.</span><span class="sxs-lookup"><span data-stu-id="d35aa-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="d35aa-232">Tenha em mente que as classes :::no-loc(Razor)::: de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d35aa-232">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d35aa-233">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="d35aa-233">Disposal of services</span></span>

<span data-ttu-id="d35aa-234">O contêiner chama <xref:System.IDisposable.Dispose%2A> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="d35aa-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d35aa-235">Os serviços resolvidos do contêiner nunca devem ser descartados pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="d35aa-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="d35aa-236">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="d35aa-237">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="d35aa-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d35aa-238">O console de depuração mostra a saída a seguir após cada atualização da página de índice:</span><span class="sxs-lookup"><span data-stu-id="d35aa-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="d35aa-239">Serviços não criados pelo contêiner de serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-239">Services not created by the service container</span></span>

<span data-ttu-id="d35aa-240">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d35aa-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="d35aa-241">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="d35aa-241">In the preceding code:</span></span>

* <span data-ttu-id="d35aa-242">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d35aa-243">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d35aa-244">O desenvolvedor é responsável por descartar os serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d35aa-245">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="d35aa-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="d35aa-246">Consulte as [diretrizes de IDisposable para a instância transitória e compartilhada](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d35aa-247">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="d35aa-247">Default service container replacement</span></span>

<span data-ttu-id="d35aa-248">Consulte [substituição do contêiner de serviço padrão](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="d35aa-249">Recomendações</span><span class="sxs-lookup"><span data-stu-id="d35aa-249">Recommendations</span></span>

<span data-ttu-id="d35aa-250">Consulte as [recomendações](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) em [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d35aa-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="d35aa-251">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="d35aa-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="d35aa-252">Por exemplo, não invoque <xref:System.IServiceProvider.GetService%2A> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="d35aa-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d35aa-253">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="d35aa-253">**Incorrect:**</span></span>

    ![Código incorreto](dependency-injection/_static/bad.png)

  <span data-ttu-id="d35aa-255">**Correto** :</span><span class="sxs-lookup"><span data-stu-id="d35aa-255">**Correct** :</span></span>

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
* <span data-ttu-id="d35aa-256">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="d35aa-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d35aa-257">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="d35aa-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="d35aa-258">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d35aa-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="d35aa-259">Evite chamadas para <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="d35aa-260">Chamar `BuildServiceProvider` normalmente acontece quando o desenvolvedor deseja resolver um serviço no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="d35aa-261">Por exemplo, considere o caso em que o `LoginPath` é carregado a partir da configuração.</span><span class="sxs-lookup"><span data-stu-id="d35aa-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="d35aa-262">Evite a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="d35aa-262">Avoid the following approach:</span></span>

  ![código inadequado chamando BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="d35aa-264">Na imagem anterior, selecionar a linha ondulada verde em `services.BuildServiceProvider` mostra o seguinte aviso de ASP0000:</span><span class="sxs-lookup"><span data-stu-id="d35aa-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="d35aa-265">ASP0000 chamar ' BuildServiceProvider ' do código do aplicativo resulta em uma cópia adicional dos serviços singleton que estão sendo criados.</span><span class="sxs-lookup"><span data-stu-id="d35aa-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="d35aa-266">Considere alternativas como a injeção de dependência de serviços como parâmetros para ' Configurar '.</span><span class="sxs-lookup"><span data-stu-id="d35aa-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="d35aa-267">`BuildServiceProvider`A chamada cria um segundo contêiner, que pode criar singletons rasgados e causar referências a grafos de objeto em vários contêineres.</span><span class="sxs-lookup"><span data-stu-id="d35aa-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="d35aa-268">Uma maneira correta de `LoginPath` se obter é usar o suporte interno do padrão de opções para di:</span><span class="sxs-lookup"><span data-stu-id="d35aa-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="d35aa-269">Serviços inposados transitórios são capturados pelo contêiner para descarte.</span><span class="sxs-lookup"><span data-stu-id="d35aa-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="d35aa-270">Isso pode transformar em um vazamento de memória se resolvido no contêiner de nível superior.</span><span class="sxs-lookup"><span data-stu-id="d35aa-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="d35aa-271">Habilite a validação de escopo para certificar-se de que o aplicativo não tem singletons que capturam serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="d35aa-272">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d35aa-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d35aa-273">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d35aa-274">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d35aa-275">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="d35aa-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d35aa-276">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="d35aa-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="d35aa-277">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="d35aa-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="d35aa-278">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) é uma estrutura de aplicativo para a criação de aplicativos modulares multilocatários em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d35aa-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="d35aa-279">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="d35aa-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="d35aa-280">Consulte os [exemplos do Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obter exemplos de como criar aplicativos modulares e multilocatários usando apenas a estrutura do Orchard Core sem qualquer um de seus recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="d35aa-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d35aa-281">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="d35aa-281">Framework-provided services</span></span>

<span data-ttu-id="d35aa-282">O `Startup.ConfigureServices` método registra os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d35aa-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d35aa-283">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d35aa-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d35aa-284">Para aplicativos baseados nos modelos de ASP.NET Core, a estrutura registra mais de 250 serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="d35aa-285">A tabela a seguir lista uma pequena amostra desses serviços registrados no Framework:</span><span class="sxs-lookup"><span data-stu-id="d35aa-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="d35aa-286">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-286">Service Type</span></span>                                                                                    | <span data-ttu-id="d35aa-287">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d35aa-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d35aa-288">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="d35aa-289">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="d35aa-290">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="d35aa-291">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="d35aa-292">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="d35aa-293">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="d35aa-294">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="d35aa-295">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="d35aa-296">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="d35aa-297">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="d35aa-298">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="d35aa-299">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="d35aa-300">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="d35aa-301">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d35aa-302">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d35aa-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="d35aa-303">Padrões de conferência NDC para desenvolvimento de aplicativo de DI</span><span class="sxs-lookup"><span data-stu-id="d35aa-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d35aa-304">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d35aa-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d35aa-305">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d35aa-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d35aa-306">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="d35aa-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d35aa-307">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d35aa-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d35aa-308">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d35aa-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d35aa-309">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d35aa-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d35aa-310">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d35aa-311">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d35aa-312">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d35aa-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d35aa-313">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d35aa-313">Overview of dependency injection</span></span>

<span data-ttu-id="d35aa-314">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d35aa-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d35aa-315">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="d35aa-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="d35aa-316">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d35aa-317">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="d35aa-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d35aa-318">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d35aa-319">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="d35aa-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d35aa-320">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d35aa-321">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d35aa-322">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d35aa-323">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d35aa-324">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d35aa-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d35aa-325">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d35aa-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d35aa-326">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="d35aa-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d35aa-327">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d35aa-328">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d35aa-329">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d35aa-330">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d35aa-331">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="d35aa-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d35aa-332">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d35aa-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d35aa-333">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d35aa-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d35aa-334">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="d35aa-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d35aa-335">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d35aa-336">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d35aa-337">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="d35aa-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d35aa-338">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência* , *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="d35aa-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="d35aa-339">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d35aa-340">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d35aa-341">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d35aa-342">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="d35aa-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d35aa-343">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d35aa-344">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d35aa-345">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d35aa-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d35aa-346">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-346">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d35aa-347">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços :::no-loc(Razor)::: e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="d35aa-347">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="d35aa-348">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="d35aa-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d35aa-349">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-349">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d35aa-350">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="d35aa-350">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="d35aa-351">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="d35aa-351">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d35aa-352">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-352">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d35aa-353">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="d35aa-353">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d35aa-354">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="d35aa-354">Services injected into Startup</span></span>

<span data-ttu-id="d35aa-355">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="d35aa-355">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d35aa-356">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d35aa-356">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d35aa-357">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-357">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d35aa-358">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="d35aa-358">Framework-provided services</span></span>

<span data-ttu-id="d35aa-359">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d35aa-359">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d35aa-360">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d35aa-360">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d35aa-361">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-361">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d35aa-362">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d35aa-362">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d35aa-363">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-363">Service Type</span></span> | <span data-ttu-id="d35aa-364">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d35aa-364">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d35aa-365">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-365">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="d35aa-366">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-366">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="d35aa-367">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-367">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d35aa-368">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-368">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d35aa-369">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-369">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d35aa-370">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d35aa-371">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-371">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d35aa-372">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-372">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d35aa-373">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-373">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d35aa-374">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-374">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d35aa-375">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-375">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d35aa-376">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-376">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d35aa-377">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-377">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d35aa-378">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-378">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d35aa-379">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="d35aa-379">Register additional services with extension methods</span></span>

<span data-ttu-id="d35aa-380">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-380">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d35aa-381">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> :</span><span class="sxs-lookup"><span data-stu-id="d35aa-381">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="d35aa-382">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="d35aa-382">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d35aa-383">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-383">Service lifetimes</span></span>

<span data-ttu-id="d35aa-384">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-384">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d35aa-385">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="d35aa-385">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d35aa-386">Transitório</span><span class="sxs-lookup"><span data-stu-id="d35aa-386">Transient</span></span>

<span data-ttu-id="d35aa-387">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-387">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d35aa-388">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-388">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d35aa-389">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-389">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d35aa-390">Com escopo</span><span class="sxs-lookup"><span data-stu-id="d35aa-390">Scoped</span></span>

<span data-ttu-id="d35aa-391">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="d35aa-391">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d35aa-392">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-392">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="d35aa-393">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-393">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d35aa-394">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="d35aa-394">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d35aa-395">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-395">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d35aa-396">Singleton</span><span class="sxs-lookup"><span data-stu-id="d35aa-396">Singleton</span></span>

<span data-ttu-id="d35aa-397">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="d35aa-397">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d35aa-398">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="d35aa-398">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d35aa-399">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-399">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d35aa-400">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-400">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="d35aa-401">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-401">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="d35aa-402">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="d35aa-402">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d35aa-403">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="d35aa-403">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d35aa-404">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="d35aa-404">Service registration methods</span></span>

<span data-ttu-id="d35aa-405">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="d35aa-405">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d35aa-406">Método</span><span class="sxs-lookup"><span data-stu-id="d35aa-406">Method</span></span> | <span data-ttu-id="d35aa-407">Automática</span><span class="sxs-lookup"><span data-stu-id="d35aa-407">Automatic</span></span><br><span data-ttu-id="d35aa-408">objeto</span><span class="sxs-lookup"><span data-stu-id="d35aa-408">object</span></span><br><span data-ttu-id="d35aa-409">descarte</span><span class="sxs-lookup"><span data-stu-id="d35aa-409">disposal</span></span> | <span data-ttu-id="d35aa-410">Vários</span><span class="sxs-lookup"><span data-stu-id="d35aa-410">Multiple</span></span><br><span data-ttu-id="d35aa-411">implementações</span><span class="sxs-lookup"><span data-stu-id="d35aa-411">implementations</span></span> | <span data-ttu-id="d35aa-412">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="d35aa-412">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d35aa-413">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d35aa-413">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d35aa-414">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-414">Yes</span></span> | <span data-ttu-id="d35aa-415">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-415">Yes</span></span> | <span data-ttu-id="d35aa-416">Não</span><span class="sxs-lookup"><span data-stu-id="d35aa-416">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d35aa-417">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d35aa-417">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="d35aa-418">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-418">Yes</span></span> | <span data-ttu-id="d35aa-419">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-419">Yes</span></span> | <span data-ttu-id="d35aa-420">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-420">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d35aa-421">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d35aa-421">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d35aa-422">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-422">Yes</span></span> | <span data-ttu-id="d35aa-423">Não</span><span class="sxs-lookup"><span data-stu-id="d35aa-423">No</span></span> | <span data-ttu-id="d35aa-424">Não</span><span class="sxs-lookup"><span data-stu-id="d35aa-424">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d35aa-425">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d35aa-425">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="d35aa-426">Não</span><span class="sxs-lookup"><span data-stu-id="d35aa-426">No</span></span> | <span data-ttu-id="d35aa-427">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-427">Yes</span></span> | <span data-ttu-id="d35aa-428">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-428">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d35aa-429">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d35aa-429">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="d35aa-430">Não</span><span class="sxs-lookup"><span data-stu-id="d35aa-430">No</span></span> | <span data-ttu-id="d35aa-431">Não</span><span class="sxs-lookup"><span data-stu-id="d35aa-431">No</span></span> | <span data-ttu-id="d35aa-432">Sim</span><span class="sxs-lookup"><span data-stu-id="d35aa-432">Yes</span></span> |

<span data-ttu-id="d35aa-433">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d35aa-433">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d35aa-434">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d35aa-434">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d35aa-435">O registro de um serviço com apenas um tipo de implementação é equivalente ao registro desse serviço com a mesma implementação e tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-435">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="d35aa-436">É por isso que várias implementações de um serviço não podem ser registradas usando os métodos que não usam um tipo de serviço explícito.</span><span class="sxs-lookup"><span data-stu-id="d35aa-436">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="d35aa-437">Esses métodos podem registrar várias *instâncias* de um serviço, mas todos terão o mesmo tipo de *implementação* .</span><span class="sxs-lookup"><span data-stu-id="d35aa-437">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="d35aa-438">Qualquer um dos métodos de registro de serviço acima pode ser usado para registrar várias instâncias de serviço do mesmo tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-438">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="d35aa-439">No exemplo a seguir, `AddSingleton` é chamado duas vezes com `IMyDependency` como o tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-439">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="d35aa-440">A segunda chamada para `AddSingleton` substitui a anterior quando resolvida como `IMyDependency` e a adiciona à anterior quando vários serviços são resolvidos por meio de `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-440">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="d35aa-441">Os serviços aparecem na ordem em que foram registrados quando resolvidos por meio de `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-441">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="d35aa-442">A estrutura também fornece `TryAdd{LIFETIME}` métodos de extensão, que registram o serviço somente se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-442">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d35aa-443">No exemplo a seguir, a chamada para `AddSingleton` registra `MyDependency` como uma implementação para `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d35aa-443">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="d35aa-444">A chamada para `TryAddSingleton` não tem efeito porque `IMyDependency` já tem uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-444">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="d35aa-445">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="d35aa-445">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d35aa-446">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="d35aa-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d35aa-447">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-447">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d35aa-448">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="d35aa-448">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d35aa-449">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="d35aa-449">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d35aa-450">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-450">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d35aa-451">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-451">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d35aa-452">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="d35aa-452">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d35aa-453">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="d35aa-453">Constructor injection behavior</span></span>

<span data-ttu-id="d35aa-454">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="d35aa-454">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d35aa-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d35aa-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d35aa-456">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-456">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d35aa-457">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="d35aa-457">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d35aa-458">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="d35aa-458">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d35aa-459">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="d35aa-459">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d35aa-460">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d35aa-460">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d35aa-461">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d35aa-461">Entity Framework contexts</span></span>

<span data-ttu-id="d35aa-462">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-462">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d35aa-463">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d35aa-463">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d35aa-464">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-464">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d35aa-465">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="d35aa-465">Lifetime and registration options</span></span>

<span data-ttu-id="d35aa-466">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-466">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d35aa-467">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="d35aa-467">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d35aa-468">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-468">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d35aa-469">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="d35aa-469">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d35aa-470">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="d35aa-470">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d35aa-471">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-471">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d35aa-472">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-472">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d35aa-473">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-473">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d35aa-474">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-474">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d35aa-475">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-475">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d35aa-476">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-476">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d35aa-477">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-477">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d35aa-478">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="d35aa-478">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d35aa-479">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-479">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d35aa-480">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="d35aa-480">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d35aa-481">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="d35aa-481">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d35aa-482">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-482">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d35aa-483">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="d35aa-483">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d35aa-484">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="d35aa-484">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d35aa-485">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="d35aa-485">**First request:**</span></span>

<span data-ttu-id="d35aa-486">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d35aa-486">Controller operations:</span></span>

<span data-ttu-id="d35aa-487">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d35aa-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d35aa-488">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d35aa-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d35aa-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d35aa-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d35aa-490">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d35aa-490">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d35aa-491">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="d35aa-491">`OperationService` operations:</span></span>

<span data-ttu-id="d35aa-492">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d35aa-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d35aa-493">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d35aa-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d35aa-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d35aa-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d35aa-495">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d35aa-495">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d35aa-496">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="d35aa-496">**Second request:**</span></span>

<span data-ttu-id="d35aa-497">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d35aa-497">Controller operations:</span></span>

<span data-ttu-id="d35aa-498">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d35aa-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d35aa-499">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d35aa-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d35aa-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d35aa-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d35aa-501">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d35aa-501">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d35aa-502">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="d35aa-502">`OperationService` operations:</span></span>

<span data-ttu-id="d35aa-503">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d35aa-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d35aa-504">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d35aa-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d35aa-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d35aa-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d35aa-506">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d35aa-506">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d35aa-507">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="d35aa-507">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d35aa-508">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="d35aa-508">*Transient* objects are always different.</span></span> <span data-ttu-id="d35aa-509">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-509">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d35aa-510">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-510">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d35aa-511">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-511">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d35aa-512">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-512">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d35aa-513">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="d35aa-513">Call services from main</span></span>

<span data-ttu-id="d35aa-514">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-514">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d35aa-515">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d35aa-515">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d35aa-516">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d35aa-516">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="d35aa-517">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="d35aa-517">Scope validation</span></span>

<span data-ttu-id="d35aa-518">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="d35aa-518">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d35aa-519">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="d35aa-519">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d35aa-520">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="d35aa-520">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d35aa-521">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-521">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d35aa-522">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-522">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d35aa-523">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="d35aa-523">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d35aa-524">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-524">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d35aa-525">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-525">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d35aa-526">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d35aa-526">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="d35aa-527">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="d35aa-527">Request Services</span></span>

<span data-ttu-id="d35aa-528">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d35aa-528">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d35aa-529">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-529">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d35aa-530">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-530">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d35aa-531">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-531">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d35aa-532">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-532">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d35aa-533">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="d35aa-533">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d35aa-534">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d35aa-534">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d35aa-535">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d35aa-535">Design services for dependency injection</span></span>

<span data-ttu-id="d35aa-536">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="d35aa-536">Best practices are to:</span></span>

* <span data-ttu-id="d35aa-537">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-537">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d35aa-538">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="d35aa-538">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d35aa-539">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="d35aa-539">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d35aa-540">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-540">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d35aa-541">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="d35aa-541">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d35aa-542">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="d35aa-542">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d35aa-543">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d35aa-543">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d35aa-544">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-544">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d35aa-545">Tenha em mente que as classes :::no-loc(Razor)::: de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d35aa-545">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d35aa-546">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d35aa-546">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d35aa-547">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="d35aa-547">Disposal of services</span></span>

<span data-ttu-id="d35aa-548">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="d35aa-548">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d35aa-549">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-549">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d35aa-550">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="d35aa-550">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="d35aa-551">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d35aa-551">In the following example:</span></span>

* <span data-ttu-id="d35aa-552">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-552">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d35aa-553">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-553">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d35aa-554">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-554">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d35aa-555">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-555">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d35aa-556">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="d35aa-556">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d35aa-557">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="d35aa-557">Transient, limited lifetime</span></span>

<span data-ttu-id="d35aa-558">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="d35aa-558">**Scenario**</span></span>

<span data-ttu-id="d35aa-559">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="d35aa-559">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d35aa-560">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="d35aa-560">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="d35aa-561">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-561">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d35aa-562">**Solução**</span><span class="sxs-lookup"><span data-stu-id="d35aa-562">**Solution**</span></span>

<span data-ttu-id="d35aa-563">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="d35aa-563">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d35aa-564">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="d35aa-564">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d35aa-565">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="d35aa-565">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d35aa-566">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="d35aa-566">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d35aa-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d35aa-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d35aa-568">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="d35aa-568">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d35aa-569">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="d35aa-569">**Scenario**</span></span>

<span data-ttu-id="d35aa-570">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="d35aa-570">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d35aa-571">**Solução**</span><span class="sxs-lookup"><span data-stu-id="d35aa-571">**Solution**</span></span>

<span data-ttu-id="d35aa-572">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="d35aa-572">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d35aa-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d35aa-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d35aa-574">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="d35aa-574">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d35aa-575">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="d35aa-575">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="d35aa-576">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="d35aa-576">General Guidelines</span></span>

* <span data-ttu-id="d35aa-577">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="d35aa-577">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d35aa-578">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="d35aa-578">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d35aa-579">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="d35aa-579">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d35aa-580">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="d35aa-580">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d35aa-581">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="d35aa-581">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d35aa-582">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="d35aa-582">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d35aa-583">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-583">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d35aa-584">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="d35aa-584">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d35aa-585">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="d35aa-585">Default service container replacement</span></span>

<span data-ttu-id="d35aa-586">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="d35aa-586">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d35aa-587">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="d35aa-587">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d35aa-588">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="d35aa-588">Property injection</span></span>
* <span data-ttu-id="d35aa-589">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="d35aa-589">Injection based on name</span></span>
* <span data-ttu-id="d35aa-590">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="d35aa-590">Child containers</span></span>
* <span data-ttu-id="d35aa-591">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="d35aa-591">Custom lifetime management</span></span>
* <span data-ttu-id="d35aa-592">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="d35aa-592">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d35aa-593">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="d35aa-593">Convention-based registration</span></span>

<span data-ttu-id="d35aa-594">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d35aa-594">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d35aa-595">Autofac</span><span class="sxs-lookup"><span data-stu-id="d35aa-595">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d35aa-596">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d35aa-596">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d35aa-597">Grace</span><span class="sxs-lookup"><span data-stu-id="d35aa-597">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d35aa-598">LightInject</span><span class="sxs-lookup"><span data-stu-id="d35aa-598">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d35aa-599">Lamar</span><span class="sxs-lookup"><span data-stu-id="d35aa-599">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d35aa-600">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d35aa-600">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d35aa-601">Unity</span><span class="sxs-lookup"><span data-stu-id="d35aa-601">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d35aa-602">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="d35aa-602">Thread safety</span></span>

<span data-ttu-id="d35aa-603">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-603">Create thread-safe singleton services.</span></span> <span data-ttu-id="d35aa-604">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="d35aa-604">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d35aa-605">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d35aa-605">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d35aa-606">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="d35aa-606">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d35aa-607">Recomendações</span><span class="sxs-lookup"><span data-stu-id="d35aa-607">Recommendations</span></span>

* <span data-ttu-id="d35aa-608">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="d35aa-608">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d35aa-609">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-609">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d35aa-610">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-610">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d35aa-611">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d35aa-611">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d35aa-612">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d35aa-612">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d35aa-613">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d35aa-613">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d35aa-614">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="d35aa-614">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d35aa-615">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="d35aa-615">Avoid static access to services.</span></span> <span data-ttu-id="d35aa-616">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="d35aa-616">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="d35aa-617">Evite usar o *padrão de localizador de serviço* , que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="d35aa-617">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="d35aa-618">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="d35aa-618">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="d35aa-619">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="d35aa-619">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="d35aa-620">**Correto** :</span><span class="sxs-lookup"><span data-stu-id="d35aa-620">**Correct** :</span></span>

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

* <span data-ttu-id="d35aa-621">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="d35aa-621">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="d35aa-622">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d35aa-622">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d35aa-623">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="d35aa-623">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d35aa-624">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="d35aa-624">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d35aa-625">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="d35aa-625">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d35aa-626">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="d35aa-626">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d35aa-627">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d35aa-627">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d35aa-628">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d35aa-628">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d35aa-629">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d35aa-629">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d35aa-630">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="d35aa-630">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d35aa-631">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d35aa-631">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d35aa-632">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d35aa-632">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
