---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678563"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="8b3d4-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b3d4-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b3d4-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="8b3d4-105">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="8b3d4-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="8b3d4-107">Para obter informações sobre como usar a injeção de dependência em aplicativos de console, consulte [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="8b3d4-108">Para obter mais informações sobre injeção de dependência de opções, consulte <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8b3d4-109">Este tópico fornece informações sobre injeção de dependência no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="8b3d4-110">Para obter informações sobre como usar a injeção de dependência em aplicativos de console, consulte [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="8b3d4-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b3d4-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="8b3d4-112">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="8b3d4-112">Overview of dependency injection</span></span>

<span data-ttu-id="8b3d4-113">Uma *dependência* é um objeto do qual outro objeto depende.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="8b3d4-114">Examine a seguinte `MyDependency` classe com um `WriteMessage` método de que outras classes dependem:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="8b3d4-115">Uma classe pode criar uma instância da `MyDependency` classe para fazer uso de seu `WriteMessage` método.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="8b3d4-116">No exemplo a seguir, a `MyDependency` classe é uma dependência da `IndexModel` classe:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="8b3d4-117">A classe cria e depende diretamente da `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="8b3d4-118">As dependências de código, como no exemplo anterior, são problemáticas e devem ser evitadas pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="8b3d4-119">Para substituir `MyDependency` por uma implementação diferente, a `IndexModel` classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="8b3d4-120">Se `MyDependency` tiver dependências, elas também deverão ser configuradas pela `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="8b3d4-121">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="8b3d4-122">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="8b3d4-123">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="8b3d4-124">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="8b3d4-125">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="8b3d4-126">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="8b3d4-127">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8b3d4-128">Normalmente, os serviços são registrados no método do aplicativo `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="8b3d4-129">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="8b3d4-130">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="8b3d4-131">No [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a `IMyDependency` interface define o `WriteMessage` método:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="8b3d4-132">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="8b3d4-133">O aplicativo de exemplo registra o `IMyDependency` serviço com o tipo concreto `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="8b3d4-134">O <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> método registra o serviço com um tempo de vida de escopo, o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="8b3d4-135">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="8b3d4-136">No aplicativo de exemplo, o `IMyDependency` serviço é solicitado e usado para chamar o `WriteMessage` método:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="8b3d4-137">Usando o padrão DI, o controlador:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="8b3d4-138">Não usa o tipo concreto `MyDependency` , apenas a `IMyDependency` interface que ele implementa.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="8b3d4-139">Isso facilita a alteração da implementação que o controlador usa sem modificar o controlador.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="8b3d4-140">Não cria uma instância do `MyDependency` , ela é criada pelo contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="8b3d4-141">A implementação da `IMyDependency` interface pode ser aprimorada usando a API de registro em log interna:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="8b3d4-142">O `ConfigureServices` método atualizado registra a nova `IMyDependency` implementação:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="8b3d4-143">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601> , que ele solicita no construtor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="8b3d4-144">`ILogger<TCategoryName>` é um [serviço fornecido pelo Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="8b3d4-145">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="8b3d4-146">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="8b3d4-147">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="8b3d4-148">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência* , *grafo de dependência* ou *grafo de objeto* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="8b3d4-149">O contêiner resolve aproveitando `ILogger<TCategoryName>` os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar cada [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="8b3d4-150">Na terminologia de injeção de dependência, um serviço:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="8b3d4-151">Normalmente é um objeto que fornece um serviço para outros objetos, como o `IMyDependency` serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="8b3d4-152">O não está relacionado a um serviço Web, embora o serviço possa usar um serviço Web.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="8b3d4-153">A estrutura fornece um sistema de [registro em log](xref:fundamentals/logging/index) robusto.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="8b3d4-154">As `IMyDependency` implementações mostradas nos exemplos anteriores foram escritas para demonstrar a di básica, não para implementar o registro em log.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="8b3d4-155">A maioria dos aplicativos não deve precisar gravar agentes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="8b3d4-156">O código a seguir demonstra como usar o log padrão, que não exige que nenhum serviço seja registrado no `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="8b3d4-157">Usando o código anterior, não há necessidade de atualizar `ConfigureServices` , porque o [registro em log](xref:fundamentals/logging/index) é fornecido pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="8b3d4-158">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="8b3d4-158">Services injected into Startup</span></span>

<span data-ttu-id="8b3d4-159">Os serviços podem ser injetados no `Startup` Construtor e no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="8b3d4-160">Somente os seguintes serviços podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="8b3d4-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8b3d4-161">Qualquer serviço registrado com o contêiner DI pode ser injetado no `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="8b3d4-162">Para obter mais informações, consulte <xref:fundamentals/startup> e [acessar a configuração na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="8b3d4-163">Registrar grupos de serviços com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="8b3d4-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="8b3d4-164">O ASP.NET Core Framework usa uma Convenção para registrar um grupo de serviços relacionados.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="8b3d4-165">A Convenção é usar um método de `Add{GROUP_NAME}` extensão único para registrar todos os serviços exigidos por um recurso de estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="8b3d4-166">Por exemplo, o <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. addcontrollers> método de extensão registra os serviços necessários para controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="8b3d4-167">O código a seguir é gerado pelo :::no-loc(Razor)::: modelo de páginas usando contas de usuário individuais e mostra como adicionar serviços adicionais ao contêiner usando os métodos de extensão <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="8b3d4-168">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-168">Service lifetimes</span></span>

<span data-ttu-id="8b3d4-169">Os serviços podem ser registrados com um dos seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="8b3d4-170">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-170">Transient</span></span>
* <span data-ttu-id="8b3d4-171">Com escopo</span><span class="sxs-lookup"><span data-stu-id="8b3d4-171">Scoped</span></span>
* <span data-ttu-id="8b3d4-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-172">Singleton</span></span>

<span data-ttu-id="8b3d4-173">As seções a seguir descrevem cada um dos tempos de vida anteriores.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="8b3d4-174">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="8b3d4-175">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-175">Transient</span></span>

<span data-ttu-id="8b3d4-176">Serviços temporários de tempo de vida são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="8b3d4-177">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="8b3d4-178">Registre serviços transitórios com <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="8b3d4-179">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="8b3d4-180">Com escopo</span><span class="sxs-lookup"><span data-stu-id="8b3d4-180">Scoped</span></span>

<span data-ttu-id="8b3d4-181">Os serviços com tempo de vida com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="8b3d4-182">Registre os serviços com escopo com <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="8b3d4-183">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="8b3d4-184">Ao usar Entity Framework Core, o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> método de extensão registra os `DbContext` tipos com um tempo de vida com escopo definido por padrão.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="8b3d4-185">**Não resolva** um serviço com escopo de um singleton e tenha cuidado para não fazê-lo indiretamente, por exemplo, por meio de um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="8b3d4-186">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="8b3d4-187">Não há problema em:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-187">It's fine to:</span></span>

<span data-ttu-id="8b3d4-188">_ Resolver um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="8b3d4-189">Resolva um serviço com escopo de outro serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="8b3d4-190">Por padrão, no ambiente de desenvolvimento, a resolução de um serviço de outro serviço com um tempo de vida maior gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="8b3d4-191">Para obter mais informações, confira [Validação de escopo](#sv).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="8b3d4-192">Para usar serviços com escopo no middleware, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="8b3d4-193">Injetar o serviço no `Invoke` método ou do middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="8b3d4-194">O uso de [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) gera uma exceção de tempo de execução porque força o serviço de escopo a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="8b3d4-195">O exemplo na seção de [Opções de tempo de vida e de registro](#lifetime-and-registration-options) demonstra a `InvokeAsync` abordagem.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="8b3d4-196">Use o [middleware baseado em fábrica](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="8b3d4-197">O middleware registrado usando essa abordagem é ativado por solicitação do cliente (conexão), que permite que os serviços com escopo sejam injetados no método do middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="8b3d4-198">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="8b3d4-199">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-199">Singleton</span></span>

<span data-ttu-id="8b3d4-200">Os serviços de vida útil singleton são criados:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="8b3d4-201">Na primeira vez que forem solicitadas.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-201">The first time they're requested.</span></span>
* <span data-ttu-id="8b3d4-202">Pelo desenvolvedor, ao fornecer uma instância de implementação diretamente para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="8b3d4-203">Essa abordagem raramente é necessária.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-203">This approach is rarely needed.</span></span>

<span data-ttu-id="8b3d4-204">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="8b3d4-205">Se o aplicativo exigir um comportamento singleton, permita que o contêiner de serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="8b3d4-206">Não implemente o padrão de design singleton e forneça código para descartar o singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="8b3d4-207">Os serviços nunca devem ser descartados pelo código que resolveu o serviço do contêiner.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="8b3d4-208">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="8b3d4-209">Registre os serviços singleton com <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="8b3d4-210">Os serviços singleton devem ser thread-safe e geralmente são usados em serviços sem estado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="8b3d4-211">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="8b3d4-212">Como a memória não é liberada até que o aplicativo seja desligado, considere o uso de memória com um serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="8b3d4-213">\* **Não** _ resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="8b3d4-214">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="8b3d4-215">É bom resolver um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="8b3d4-216">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-216">Service registration methods</span></span>

<span data-ttu-id="8b3d4-217">A estrutura fornece métodos de extensão de registro de serviço que são úteis em cenários específicos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="8b3d4-218">Método</span><span class="sxs-lookup"><span data-stu-id="8b3d4-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="8b3d4-219">Automática</span><span class="sxs-lookup"><span data-stu-id="8b3d4-219">Automatic</span></span><br><span data-ttu-id="8b3d4-220">objeto</span><span class="sxs-lookup"><span data-stu-id="8b3d4-220">object</span></span><br><span data-ttu-id="8b3d4-221">descarte</span><span class="sxs-lookup"><span data-stu-id="8b3d4-221">disposal</span></span> | <span data-ttu-id="8b3d4-222">Vários</span><span class="sxs-lookup"><span data-stu-id="8b3d4-222">Multiple</span></span><br><span data-ttu-id="8b3d4-223">implementações</span><span class="sxs-lookup"><span data-stu-id="8b3d4-223">implementations</span></span> | <span data-ttu-id="8b3d4-224">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="8b3d4-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="8b3d4-225">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="8b3d4-226">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-226">Yes</span></span>                             | <span data-ttu-id="8b3d4-227">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-227">Yes</span></span>                         | <span data-ttu-id="8b3d4-228">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="8b3d4-229">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="8b3d4-230">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-230">Yes</span></span>                             | <span data-ttu-id="8b3d4-231">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-231">Yes</span></span>                         | <span data-ttu-id="8b3d4-232">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="8b3d4-233">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="8b3d4-234">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-234">Yes</span></span>                             | <span data-ttu-id="8b3d4-235">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-235">No</span></span>                          | <span data-ttu-id="8b3d4-236">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="8b3d4-237">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="8b3d4-238">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-238">No</span></span>                              | <span data-ttu-id="8b3d4-239">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-239">Yes</span></span>                         | <span data-ttu-id="8b3d4-240">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="8b3d4-241">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="8b3d4-242">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-242">No</span></span>                              | <span data-ttu-id="8b3d4-243">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-243">No</span></span>                          | <span data-ttu-id="8b3d4-244">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-244">Yes</span></span>       |

<span data-ttu-id="8b3d4-245">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="8b3d4-246">É comum usar várias implementações ao [simular tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="8b3d4-247">O registro de um serviço com apenas um tipo de implementação é equivalente ao registro desse serviço com a mesma implementação e tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="8b3d4-248">É por isso que várias implementações de um serviço não podem ser registradas usando os métodos que não usam um tipo de serviço explícito.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="8b3d4-249">Esses métodos podem registrar vários _instances \* de um serviço, mas todos terão o mesmo tipo de *implementação* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="8b3d4-250">Qualquer um dos métodos de registro de serviço acima pode ser usado para registrar várias instâncias de serviço do mesmo tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="8b3d4-251">No exemplo a seguir, `AddSingleton` é chamado duas vezes com `IMyDependency` como o tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="8b3d4-252">A segunda chamada para `AddSingleton` substitui a anterior quando resolvida como `IMyDependency` e a adiciona à anterior quando vários serviços são resolvidos por meio de `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="8b3d4-253">Os serviços aparecem na ordem em que foram registrados quando resolvidos por meio de `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="8b3d4-254">A estrutura também fornece `TryAdd{LIFETIME}` métodos de extensão, que registram o serviço somente se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="8b3d4-255">No exemplo a seguir, a chamada para `AddSingleton` registra `MyDependency` como uma implementação para `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="8b3d4-256">A chamada para `TryAddSingleton` não tem efeito porque `IMyDependency` já tem uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="8b3d4-257">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="8b3d4-258">Os métodos [TryAddEnumerable (Service Descriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registram o serviço somente se ainda não houver uma implementação *do mesmo tipo* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="8b3d4-259">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="8b3d4-260">Ao registrar serviços, o desenvolvedor deve adicionar uma instância se um do mesmo tipo ainda não tiver sido adicionado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="8b3d4-261">Em geral, os autores `TryAddEnumerable` de biblioteca usam para evitar o registro de várias cópias de uma implementação no contêiner.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="8b3d4-262">No exemplo a seguir, a primeira chamada para `TryAddEnumerable` registra `MyDependency` como uma implementação para `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="8b3d4-263">A segunda chamada é registrada `MyDependency` para `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="8b3d4-264">A terceira chamada não tem nenhum efeito porque `IMyDependency1` já tem uma implementação registrada de `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="8b3d4-265">O registro de serviço geralmente é independente de ordem, exceto ao registrar várias implementações do mesmo tipo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="8b3d4-266">`IServiceCollection` é uma coleção de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objetos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="8b3d4-267">O exemplo a seguir mostra como registrar um serviço criando e adicionando um `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="8b3d4-268">Os `Add{LIFETIME}` métodos internos usam a mesma abordagem.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="8b3d4-269">Por exemplo, consulte o [código-fonte de Addscoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="8b3d4-270">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="8b3d4-270">Constructor injection behavior</span></span>

<span data-ttu-id="8b3d4-271">Os serviços podem ser resolvidos usando:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="8b3d4-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="8b3d4-273">Cria objetos que não estão registrados no contêiner.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="8b3d4-274">Usado com recursos de estrutura, como [auxiliares de marca](xref:mvc/views/tag-helpers/intro), controladores MVC e [ASSOCIADORES de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="8b3d4-275">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="8b3d4-276">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="8b3d4-277">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="8b3d4-278">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="8b3d4-279">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8b3d4-279">Entity Framework contexts</span></span>

<span data-ttu-id="8b3d4-280">Por padrão, os contextos de Entity Framework são adicionados ao contêiner de serviço usando o [tempo de vida do escopo](#service-lifetimes) porque as operações de banco de dados do aplicativo Web normalmente são delimitadas à solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="8b3d4-281">Para usar um tempo de vida diferente, especifique o tempo de vida usando uma <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="8b3d4-282">Os serviços de um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida menor do que o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="8b3d4-283">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="8b3d4-283">Lifetime and registration options</span></span>

<span data-ttu-id="8b3d4-284">Para demonstrar a diferença entre tempos de vida de serviço e suas opções de registro, considere as seguintes interfaces que representam uma tarefa como uma operação com um identificador, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="8b3d4-285">Dependendo de como o tempo de vida do serviço de uma operação está configurado para as seguintes interfaces, o contêiner fornecerá as mesmas instâncias do serviço ou as mesmas quando solicitado por uma classe:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="8b3d4-286">A classe a seguir `Operation` implementa todas as interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="8b3d4-287">O `Operation` Construtor gera um GUID e armazena os quatro últimos caracteres na `OperationId` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="8b3d4-288">O `Startup.ConfigureServices` método cria vários registros da classe de `Operation` acordo com os tempos de vida nomeados:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="8b3d4-289">O aplicativo de exemplo demonstra tempos de vida de objeto dentro e entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="8b3d4-290">O `IndexModel` e o middleware solicitam cada tipo de `IOperation` tipo e registram o `OperationId` para cada um:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="8b3d4-291">Semelhante ao `IndexModel` , o middleware resolve os mesmos serviços:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="8b3d4-292">Os serviços com escopo devem ser resolvidos no `InvokeAsync` método:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="8b3d4-293">A saída do agente mostra:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-293">The logger output shows:</span></span>

* <span data-ttu-id="8b3d4-294">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-294">*Transient* objects are always different.</span></span> <span data-ttu-id="8b3d4-295">O `OperationId` valor transitório é diferente no `IndexModel` e no middleware.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="8b3d4-296">Os objetos com *escopo* são os mesmos para cada solicitação, mas diferentes em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="8b3d4-297">Os objetos *singleton* são os mesmos para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="8b3d4-298">Para reduzir a saída de log, defina "log: LogLevel: Microsoft: Error" na *appsettings.Development.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="8b3d4-299">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="8b3d4-299">Call services from main</span></span>

<span data-ttu-id="8b3d4-300">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="8b3d4-301">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="8b3d4-302">O exemplo a seguir mostra como acessar o serviço com escopo `IMyDependency` e chamar seu `WriteMessage` método em `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="8b3d4-303">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="8b3d4-303">Scope validation</span></span>

<span data-ttu-id="8b3d4-304">Quando o aplicativo é executado no [ambiente de desenvolvimento](xref:fundamentals/environments) e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8b3d4-305">Os serviços com escopo não são resolvidos do provedor de serviços raiz.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="8b3d4-306">Os serviços com escopo não são injetados em singletons.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="8b3d4-307">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> é chamado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="8b3d4-308">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo quando o provedor começa com o aplicativo e é Descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8b3d4-309">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8b3d4-310">Se um serviço com escopo for criado no contêiner raiz, o tempo de vida do serviço será efetivamente promovido para singleton porque é descartado apenas pelo contêiner raiz quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="8b3d4-311">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8b3d4-312">Para obter mais informações, confira [Validação de escopo](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="8b3d4-313">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="8b3d4-313">Request Services</span></span>

<span data-ttu-id="8b3d4-314">Os serviços disponíveis em uma solicitação de ASP.NET Core são expostos por meio da coleção [HttpContext. Requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="8b3d4-315">Quando os serviços são solicitados de dentro de uma solicitação, os serviços e suas dependências são resolvidos na `RequestServices` coleção.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="8b3d4-316">A estrutura cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="8b3d4-317">Todos os serviços com escopo são válidos enquanto a solicitação está ativa.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="8b3d4-318">Prefira solicitar dependências como parâmetros de construtor para resolver serviços da `RequestServices` coleção.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="8b3d4-319">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="8b3d4-320">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="8b3d4-320">Design services for dependency injection</span></span>

<span data-ttu-id="8b3d4-321">Ao projetar serviços para injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="8b3d4-322">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="8b3d4-323">Evite criar o estado global criando aplicativos para usar os serviços singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="8b3d4-324">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="8b3d4-325">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="8b3d4-326">Torne os serviços pequenos, bem fatorados e facilmente testados.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="8b3d4-327">Se uma classe tiver muitas dependências injetadas, pode ser um sinal de que a classe tem muitas responsabilidades e viola o [princípio de responsabilidade única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="8b3d4-328">Tente refatorar a classe movendo algumas de suas responsabilidades para novas classes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="8b3d4-329">Tenha em mente que as classes :::no-loc(Razor)::: de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-329">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="8b3d4-330">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="8b3d4-330">Disposal of services</span></span>

<span data-ttu-id="8b3d4-331">O contêiner chama <xref:System.IDisposable.Dispose%2A> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="8b3d4-332">Os serviços resolvidos do contêiner nunca devem ser descartados pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="8b3d4-333">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="8b3d4-334">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8b3d4-335">O console de depuração mostra a saída a seguir após cada atualização da página de índice:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="8b3d4-336">Serviços não criados pelo contêiner de serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-336">Services not created by the service container</span></span>

<span data-ttu-id="8b3d4-337">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="8b3d4-338">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-338">In the preceding code:</span></span>

* <span data-ttu-id="8b3d4-339">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="8b3d4-340">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="8b3d4-341">O desenvolvedor é responsável por descartar os serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="8b3d4-342">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="8b3d4-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="8b3d4-343">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="8b3d4-343">Transient, limited lifetime</span></span>

<span data-ttu-id="8b3d4-344">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-344">**Scenario**</span></span>

<span data-ttu-id="8b3d4-345">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="8b3d4-346">A instância é resolvida no escopo raiz (contêiner raiz).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="8b3d4-347">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="8b3d4-348">**Solução**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-348">**Solution**</span></span>

<span data-ttu-id="8b3d4-349">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="8b3d4-350">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="8b3d4-351">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="8b3d4-352">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="8b3d4-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="8b3d4-354">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="8b3d4-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="8b3d4-355">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-355">**Scenario**</span></span>

<span data-ttu-id="8b3d4-356">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada em vários serviços, mas a <xref:System.IDisposable> instância deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="8b3d4-357">**Solução**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-357">**Solution**</span></span>

<span data-ttu-id="8b3d4-358">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="8b3d4-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="8b3d4-360">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="8b3d4-361">Descarte o escopo quando ele não for mais necessário.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="8b3d4-362">Diretrizes gerais de IDisposable</span><span class="sxs-lookup"><span data-stu-id="8b3d4-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="8b3d4-363">Não registre <xref:System.IDisposable> instâncias com um tempo de vida transitório.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="8b3d4-364">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="8b3d4-365">Não resolva <xref:System.IDisposable> instâncias com um tempo de vida transitório ou com escopo definido no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="8b3d4-366">A única exceção a isso é se o aplicativo cria/recria e descarta <xref:System.IServiceProvider> , mas esse não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="8b3d4-367">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="8b3d4-368">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="8b3d4-369">Use escopos para controlar os tempos de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="8b3d4-370">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="8b3d4-371">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="8b3d4-371">Default service container replacement</span></span>

<span data-ttu-id="8b3d4-372">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="8b3d4-373">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual ele não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="8b3d4-374">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="8b3d4-374">Property injection</span></span>
* <span data-ttu-id="8b3d4-375">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="8b3d4-375">Injection based on name</span></span>
* <span data-ttu-id="8b3d4-376">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="8b3d4-376">Child containers</span></span>
* <span data-ttu-id="8b3d4-377">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="8b3d4-377">Custom lifetime management</span></span>
* <span data-ttu-id="8b3d4-378">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="8b3d4-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="8b3d4-379">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="8b3d4-379">Convention-based registration</span></span>

<span data-ttu-id="8b3d4-380">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="8b3d4-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="8b3d4-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="8b3d4-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="8b3d4-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="8b3d4-383">Grace</span><span class="sxs-lookup"><span data-stu-id="8b3d4-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="8b3d4-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="8b3d4-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="8b3d4-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="8b3d4-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="8b3d4-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="8b3d4-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="8b3d4-387">Unity</span><span class="sxs-lookup"><span data-stu-id="8b3d4-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="8b3d4-388">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="8b3d4-388">Thread safety</span></span>

<span data-ttu-id="8b3d4-389">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="8b3d4-390">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também poderá exigir a segurança do thread dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="8b3d4-391">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="8b3d4-392">Como um construtor de tipo ( `static` ), é garantido que ele seja chamado apenas uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8b3d4-393">Recomendações</span><span class="sxs-lookup"><span data-stu-id="8b3d4-393">Recommendations</span></span>

* <span data-ttu-id="8b3d4-394">`async/await` e a `Task` resolução de serviço baseada não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="8b3d4-395">Como o C# não dá suporte a construtores assíncronos, use métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="8b3d4-396">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="8b3d4-397">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="8b3d4-398">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8b3d4-399">Da mesma forma, evite objetos de "portador de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="8b3d4-400">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="8b3d4-401">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-401">Avoid static access to services.</span></span> <span data-ttu-id="8b3d4-402">Por exemplo, evite capturar [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) como um campo ou propriedade estática para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="8b3d4-403">Mantenha as fábricas de injeção rápida e síncrona.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="8b3d4-404">Evite usar o *padrão do localizador de serviço* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-404">Avoid using the *service locator pattern* .</span></span> <span data-ttu-id="8b3d4-405">Por exemplo, não invoque <xref:System.IServiceProvider.GetService%2A> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="8b3d4-406">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-406">**Incorrect:**</span></span>

    ![Código incorreto](dependency-injection/_static/bad.png)

  <span data-ttu-id="8b3d4-408">**Correto** :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-408">**Correct** :</span></span>

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
* <span data-ttu-id="8b3d4-409">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="8b3d4-410">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="8b3d4-411">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="8b3d4-412">Evite chamadas para <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="8b3d4-413">Chamar `BuildServiceProvider` normalmente acontece quando o desenvolvedor deseja resolver um serviço no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="8b3d4-414">Por exemplo, considere o caso em que o `LoginPath` é carregado a partir da configuração.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="8b3d4-415">Evite a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-415">Avoid the following approach:</span></span>

  ![código inadequado chamando BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="8b3d4-417">Na imagem anterior, selecionar a linha ondulada verde em `services.BuildServiceProvider` mostra o seguinte aviso de ASP0000:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="8b3d4-418">ASP0000 chamar ' BuildServiceProvider ' do código do aplicativo resulta em uma cópia adicional dos serviços singleton que estão sendo criados.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="8b3d4-419">Considere alternativas como a injeção de dependência de serviços como parâmetros para ' Configurar '.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="8b3d4-420">`BuildServiceProvider`A chamada cria um segundo contêiner, que pode criar singletons rasgados e causar referências a grafos de objeto em vários contêineres.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="8b3d4-421">Uma maneira correta de `LoginPath` se obter é usar o suporte interno do padrão de opções para di:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="8b3d4-422">Serviços inposados transitórios são capturados pelo contêiner para descarte.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="8b3d4-423">Isso pode transformar em um vazamento de memória se resolvido no contêiner de nível superior.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="8b3d4-424">Habilite a validação de escopo para certificar-se de que o aplicativo não tem singletons que capturam serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="8b3d4-425">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="8b3d4-426">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="8b3d4-427">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="8b3d4-428">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="8b3d4-429">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="8b3d4-430">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="8b3d4-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="8b3d4-431">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) é uma estrutura de aplicativo para a criação de aplicativos modulares multilocatários em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="8b3d4-432">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="8b3d4-433">Consulte os [exemplos do Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obter exemplos de como criar aplicativos modulares e multilocatários usando apenas a estrutura do Orchard Core sem qualquer um de seus recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8b3d4-434">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="8b3d4-434">Framework-provided services</span></span>

<span data-ttu-id="8b3d4-435">O `Startup.ConfigureServices` método registra os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="8b3d4-436">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="8b3d4-437">Para aplicativos baseados nos modelos de ASP.NET Core, a estrutura registra mais de 250 serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="8b3d4-438">A tabela a seguir lista uma pequena amostra desses serviços registrados no Framework:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="8b3d4-439">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-439">Service Type</span></span>                                                                                    | <span data-ttu-id="8b3d4-440">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="8b3d4-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="8b3d4-441">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="8b3d4-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="8b3d4-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="8b3d4-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="8b3d4-445">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="8b3d4-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="8b3d4-447">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="8b3d4-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="8b3d4-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="8b3d4-450">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="8b3d4-451">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="8b3d4-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="8b3d4-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="8b3d4-454">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8b3d4-455">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8b3d4-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="8b3d4-456">Padrões de conferência NDC para desenvolvimento de aplicativo de DI</span><span class="sxs-lookup"><span data-stu-id="8b3d4-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="8b3d4-457">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b3d4-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="8b3d4-458">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="8b3d4-459">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="8b3d4-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="8b3d4-460">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="8b3d4-461">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b3d4-461">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b3d4-462">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="8b3d4-463">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="8b3d4-464">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="8b3d4-465">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b3d4-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="8b3d4-466">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="8b3d4-466">Overview of dependency injection</span></span>

<span data-ttu-id="8b3d4-467">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="8b3d4-468">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="8b3d4-469">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="8b3d4-470">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="8b3d4-471">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="8b3d4-472">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="8b3d4-473">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="8b3d4-474">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="8b3d4-475">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="8b3d4-476">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="8b3d4-477">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="8b3d4-478">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="8b3d4-479">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="8b3d4-480">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="8b3d4-481">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8b3d4-482">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="8b3d4-483">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="8b3d4-484">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="8b3d4-485">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="8b3d4-486">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="8b3d4-487">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="8b3d4-488">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="8b3d4-489">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="8b3d4-490">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="8b3d4-491">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência* , *grafo de dependência* ou *grafo de objeto* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="8b3d4-492">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="8b3d4-493">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b3d4-494">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="8b3d4-495">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="8b3d4-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="8b3d4-496">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="8b3d4-497">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="8b3d4-498">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="8b3d4-499">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="8b3d4-500">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços :::no-loc(Razor)::: e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-500">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="8b3d4-501">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="8b3d4-502">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="8b3d4-503">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="8b3d4-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="8b3d4-504">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="8b3d4-505">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="8b3d4-506">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="8b3d4-507">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="8b3d4-507">Services injected into Startup</span></span>

<span data-ttu-id="8b3d4-508">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="8b3d4-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8b3d4-509">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="8b3d4-510">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8b3d4-511">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="8b3d4-511">Framework-provided services</span></span>

<span data-ttu-id="8b3d4-512">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="8b3d4-513">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="8b3d4-514">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="8b3d4-515">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="8b3d4-516">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-516">Service Type</span></span> | <span data-ttu-id="8b3d4-517">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="8b3d4-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="8b3d4-518">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="8b3d4-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="8b3d4-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="8b3d4-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="8b3d4-522">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="8b3d4-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="8b3d4-524">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="8b3d4-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="8b3d4-526">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="8b3d4-527">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="8b3d4-528">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="8b3d4-529">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="8b3d4-530">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="8b3d4-531">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="8b3d4-532">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="8b3d4-532">Register additional services with extension methods</span></span>

<span data-ttu-id="8b3d4-533">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="8b3d4-534">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

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

<span data-ttu-id="8b3d4-535">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="8b3d4-536">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-536">Service lifetimes</span></span>

<span data-ttu-id="8b3d4-537">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="8b3d4-538">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="8b3d4-539">Transitório</span><span class="sxs-lookup"><span data-stu-id="8b3d4-539">Transient</span></span>

<span data-ttu-id="8b3d4-540">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="8b3d4-541">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="8b3d4-542">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="8b3d4-543">Com escopo</span><span class="sxs-lookup"><span data-stu-id="8b3d4-543">Scoped</span></span>

<span data-ttu-id="8b3d4-544">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="8b3d4-545">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="8b3d4-546">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="8b3d4-547">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="8b3d4-548">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="8b3d4-549">Singleton</span><span class="sxs-lookup"><span data-stu-id="8b3d4-549">Singleton</span></span>

<span data-ttu-id="8b3d4-550">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="8b3d4-551">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="8b3d4-552">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="8b3d4-553">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="8b3d4-554">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="8b3d4-555">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="8b3d4-556">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="8b3d4-557">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="8b3d4-557">Service registration methods</span></span>

<span data-ttu-id="8b3d4-558">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="8b3d4-559">Método</span><span class="sxs-lookup"><span data-stu-id="8b3d4-559">Method</span></span> | <span data-ttu-id="8b3d4-560">Automática</span><span class="sxs-lookup"><span data-stu-id="8b3d4-560">Automatic</span></span><br><span data-ttu-id="8b3d4-561">objeto</span><span class="sxs-lookup"><span data-stu-id="8b3d4-561">object</span></span><br><span data-ttu-id="8b3d4-562">descarte</span><span class="sxs-lookup"><span data-stu-id="8b3d4-562">disposal</span></span> | <span data-ttu-id="8b3d4-563">Vários</span><span class="sxs-lookup"><span data-stu-id="8b3d4-563">Multiple</span></span><br><span data-ttu-id="8b3d4-564">implementações</span><span class="sxs-lookup"><span data-stu-id="8b3d4-564">implementations</span></span> | <span data-ttu-id="8b3d4-565">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="8b3d4-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="8b3d4-566">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="8b3d4-567">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-567">Yes</span></span> | <span data-ttu-id="8b3d4-568">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-568">Yes</span></span> | <span data-ttu-id="8b3d4-569">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="8b3d4-570">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="8b3d4-571">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-571">Yes</span></span> | <span data-ttu-id="8b3d4-572">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-572">Yes</span></span> | <span data-ttu-id="8b3d4-573">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="8b3d4-574">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="8b3d4-575">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-575">Yes</span></span> | <span data-ttu-id="8b3d4-576">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-576">No</span></span> | <span data-ttu-id="8b3d4-577">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="8b3d4-578">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="8b3d4-579">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-579">No</span></span> | <span data-ttu-id="8b3d4-580">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-580">Yes</span></span> | <span data-ttu-id="8b3d4-581">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="8b3d4-582">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="8b3d4-583">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-583">No</span></span> | <span data-ttu-id="8b3d4-584">Não</span><span class="sxs-lookup"><span data-stu-id="8b3d4-584">No</span></span> | <span data-ttu-id="8b3d4-585">Sim</span><span class="sxs-lookup"><span data-stu-id="8b3d4-585">Yes</span></span> |

<span data-ttu-id="8b3d4-586">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="8b3d4-587">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="8b3d4-588">O registro de um serviço com apenas um tipo de implementação é equivalente ao registro desse serviço com a mesma implementação e tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="8b3d4-589">É por isso que várias implementações de um serviço não podem ser registradas usando os métodos que não usam um tipo de serviço explícito.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="8b3d4-590">Esses métodos podem registrar várias *instâncias* de um serviço, mas todos terão o mesmo tipo de *implementação* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="8b3d4-591">Qualquer um dos métodos de registro de serviço acima pode ser usado para registrar várias instâncias de serviço do mesmo tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="8b3d4-592">No exemplo a seguir, `AddSingleton` é chamado duas vezes com `IMyDependency` como o tipo de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="8b3d4-593">A segunda chamada para `AddSingleton` substitui a anterior quando resolvida como `IMyDependency` e a adiciona à anterior quando vários serviços são resolvidos por meio de `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="8b3d4-594">Os serviços aparecem na ordem em que foram registrados quando resolvidos por meio de `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="8b3d4-595">A estrutura também fornece `TryAdd{LIFETIME}` métodos de extensão, que registram o serviço somente se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="8b3d4-596">No exemplo a seguir, a chamada para `AddSingleton` registra `MyDependency` como uma implementação para `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="8b3d4-597">A chamada para `TryAddSingleton` não tem efeito porque `IMyDependency` já tem uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="8b3d4-598">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="8b3d4-599">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="8b3d4-600">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="8b3d4-601">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="8b3d4-602">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="8b3d4-603">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="8b3d4-604">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="8b3d4-605">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="8b3d4-606">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="8b3d4-606">Constructor injection behavior</span></span>

<span data-ttu-id="8b3d4-607">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="8b3d4-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="8b3d4-609">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="8b3d4-610">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="8b3d4-611">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="8b3d4-612">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="8b3d4-613">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="8b3d4-614">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8b3d4-614">Entity Framework contexts</span></span>

<span data-ttu-id="8b3d4-615">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="8b3d4-616">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="8b3d4-617">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="8b3d4-618">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="8b3d4-618">Lifetime and registration options</span></span>

<span data-ttu-id="8b3d4-619">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="8b3d4-620">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="8b3d4-621">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="8b3d4-622">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="8b3d4-623">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="8b3d4-624">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="8b3d4-625">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="8b3d4-626">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="8b3d4-627">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="8b3d4-628">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="8b3d4-629">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="8b3d4-630">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="8b3d4-631">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="8b3d4-632">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="8b3d4-633">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="8b3d4-634">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="8b3d4-635">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="8b3d4-636">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="8b3d4-637">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="8b3d4-638">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-638">**First request:**</span></span>

<span data-ttu-id="8b3d4-639">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-639">Controller operations:</span></span>

<span data-ttu-id="8b3d4-640">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="8b3d4-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="8b3d4-641">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8b3d4-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8b3d4-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8b3d4-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8b3d4-643">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8b3d4-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8b3d4-644">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-644">`OperationService` operations:</span></span>

<span data-ttu-id="8b3d4-645">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="8b3d4-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="8b3d4-646">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8b3d4-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8b3d4-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8b3d4-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8b3d4-648">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8b3d4-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8b3d4-649">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-649">**Second request:**</span></span>

<span data-ttu-id="8b3d4-650">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-650">Controller operations:</span></span>

<span data-ttu-id="8b3d4-651">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="8b3d4-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="8b3d4-652">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8b3d4-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8b3d4-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8b3d4-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8b3d4-654">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8b3d4-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8b3d4-655">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-655">`OperationService` operations:</span></span>

<span data-ttu-id="8b3d4-656">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="8b3d4-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="8b3d4-657">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8b3d4-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8b3d4-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8b3d4-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8b3d4-659">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8b3d4-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8b3d4-660">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="8b3d4-661">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-661">*Transient* objects are always different.</span></span> <span data-ttu-id="8b3d4-662">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="8b3d4-663">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="8b3d4-664">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="8b3d4-665">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="8b3d4-666">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="8b3d4-666">Call services from main</span></span>

<span data-ttu-id="8b3d4-667">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="8b3d4-668">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="8b3d4-669">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="8b3d4-670">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="8b3d4-670">Scope validation</span></span>

<span data-ttu-id="8b3d4-671">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8b3d4-672">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8b3d4-673">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8b3d4-674">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="8b3d4-675">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8b3d4-676">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8b3d4-677">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8b3d4-678">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8b3d4-679">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="8b3d4-680">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="8b3d4-680">Request Services</span></span>

<span data-ttu-id="8b3d4-681">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="8b3d4-682">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="8b3d4-683">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="8b3d4-684">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="8b3d4-685">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="8b3d4-686">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="8b3d4-687">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="8b3d4-688">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="8b3d4-688">Design services for dependency injection</span></span>

<span data-ttu-id="8b3d4-689">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-689">Best practices are to:</span></span>

* <span data-ttu-id="8b3d4-690">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="8b3d4-691">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="8b3d4-692">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="8b3d4-693">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="8b3d4-694">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="8b3d4-695">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="8b3d4-696">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="8b3d4-697">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="8b3d4-698">Tenha em mente que as classes :::no-loc(Razor)::: de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-698">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="8b3d4-699">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="8b3d4-700">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="8b3d4-700">Disposal of services</span></span>

<span data-ttu-id="8b3d4-701">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="8b3d4-702">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="8b3d4-703">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="8b3d4-704">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-704">In the following example:</span></span>

* <span data-ttu-id="8b3d4-705">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="8b3d4-706">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="8b3d4-707">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="8b3d4-708">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="8b3d4-709">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="8b3d4-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="8b3d4-710">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="8b3d4-710">Transient, limited lifetime</span></span>

<span data-ttu-id="8b3d4-711">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-711">**Scenario**</span></span>

<span data-ttu-id="8b3d4-712">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="8b3d4-713">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="8b3d4-714">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="8b3d4-715">**Solução**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-715">**Solution**</span></span>

<span data-ttu-id="8b3d4-716">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="8b3d4-717">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="8b3d4-718">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="8b3d4-719">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="8b3d4-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="8b3d4-721">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="8b3d4-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="8b3d4-722">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-722">**Scenario**</span></span>

<span data-ttu-id="8b3d4-723">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="8b3d4-724">**Solução**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-724">**Solution**</span></span>

<span data-ttu-id="8b3d4-725">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="8b3d4-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="8b3d4-727">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="8b3d4-728">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="8b3d4-729">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="8b3d4-729">General Guidelines</span></span>

* <span data-ttu-id="8b3d4-730">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="8b3d4-731">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="8b3d4-732">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="8b3d4-733">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="8b3d4-734">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="8b3d4-735">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="8b3d4-736">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="8b3d4-737">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="8b3d4-738">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="8b3d4-738">Default service container replacement</span></span>

<span data-ttu-id="8b3d4-739">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="8b3d4-740">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="8b3d4-741">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="8b3d4-741">Property injection</span></span>
* <span data-ttu-id="8b3d4-742">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="8b3d4-742">Injection based on name</span></span>
* <span data-ttu-id="8b3d4-743">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="8b3d4-743">Child containers</span></span>
* <span data-ttu-id="8b3d4-744">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="8b3d4-744">Custom lifetime management</span></span>
* <span data-ttu-id="8b3d4-745">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="8b3d4-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="8b3d4-746">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="8b3d4-746">Convention-based registration</span></span>

<span data-ttu-id="8b3d4-747">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="8b3d4-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="8b3d4-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="8b3d4-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="8b3d4-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="8b3d4-750">Grace</span><span class="sxs-lookup"><span data-stu-id="8b3d4-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="8b3d4-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="8b3d4-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="8b3d4-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="8b3d4-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="8b3d4-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="8b3d4-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="8b3d4-754">Unity</span><span class="sxs-lookup"><span data-stu-id="8b3d4-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="8b3d4-755">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="8b3d4-755">Thread safety</span></span>

<span data-ttu-id="8b3d4-756">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="8b3d4-757">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="8b3d4-758">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="8b3d4-759">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8b3d4-760">Recomendações</span><span class="sxs-lookup"><span data-stu-id="8b3d4-760">Recommendations</span></span>

* <span data-ttu-id="8b3d4-761">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="8b3d4-762">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="8b3d4-763">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="8b3d4-764">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="8b3d4-765">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8b3d4-766">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="8b3d4-767">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="8b3d4-768">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-768">Avoid static access to services.</span></span> <span data-ttu-id="8b3d4-769">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="8b3d4-770">Evite usar o *padrão de localizador de serviço* , que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="8b3d4-771">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="8b3d4-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="8b3d4-772">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="8b3d4-772">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="8b3d4-773">**Correto** :</span><span class="sxs-lookup"><span data-stu-id="8b3d4-773">**Correct** :</span></span>

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

* <span data-ttu-id="8b3d4-774">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="8b3d4-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="8b3d4-775">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="8b3d4-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="8b3d4-776">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="8b3d4-777">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="8b3d4-778">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="8b3d4-779">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="8b3d4-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b3d4-780">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8b3d4-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="8b3d4-781">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b3d4-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="8b3d4-782">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="8b3d4-783">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="8b3d4-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="8b3d4-784">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="8b3d4-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="8b3d4-785">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b3d4-785">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
