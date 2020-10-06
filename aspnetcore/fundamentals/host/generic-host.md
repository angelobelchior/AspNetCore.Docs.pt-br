---
title: Host genérico .NET no ASP.NET Core
author: rick-anderson
description: Use o host genérico do .NET Core em aplicativos ASP.NET Core.  O host genérico é responsável pela inicialização do aplicativo e pelo gerenciamento de tempo de vida.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762315"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="ebe70-104">Host genérico .NET no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebe70-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ebe70-105">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="ebe70-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="ebe70-106">Este tópico fornece informações sobre como usar o host genérico .NET no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebe70-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="ebe70-107">Para obter informações sobre como usar o host genérico .NET em aplicativos de console, consulte [host genérico .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="ebe70-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="ebe70-108">Definição de host</span><span class="sxs-lookup"><span data-stu-id="ebe70-108">Host definition</span></span>

<span data-ttu-id="ebe70-109">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="ebe70-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ebe70-110">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="ebe70-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="ebe70-111">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="ebe70-111">Logging</span></span>
* <span data-ttu-id="ebe70-112">Configuração</span><span class="sxs-lookup"><span data-stu-id="ebe70-112">Configuration</span></span>
* <span data-ttu-id="ebe70-113">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="ebe70-113">`IHostedService` implementations</span></span>

<span data-ttu-id="ebe70-114">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="ebe70-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="ebe70-115">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ebe70-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ebe70-116">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ebe70-117">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="ebe70-117">Set up a host</span></span>

<span data-ttu-id="ebe70-118">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ebe70-119">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-119">The `Main` method:</span></span>

* <span data-ttu-id="ebe70-120">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ebe70-121">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ebe70-122">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="ebe70-123">O código a seguir cria uma carga de trabalho não HTTP com uma `IHostedService` implementação adicionada ao contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="ebe70-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="ebe70-124">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ebe70-125">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ebe70-126">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ebe70-127">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ebe70-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ebe70-128">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="ebe70-128">Default builder settings</span></span>

<span data-ttu-id="ebe70-129">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="ebe70-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="ebe70-130">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="ebe70-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="ebe70-131">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="ebe70-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="ebe70-132">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="ebe70-133">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ebe70-133">Command-line arguments.</span></span>
* <span data-ttu-id="ebe70-134">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="ebe70-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="ebe70-135">*appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="ebe70-136">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ebe70-137">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ebe70-138">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-138">Environment variables.</span></span>
  * <span data-ttu-id="ebe70-139">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ebe70-139">Command-line arguments.</span></span>
* <span data-ttu-id="ebe70-140">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="ebe70-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ebe70-141">Console</span><span class="sxs-lookup"><span data-stu-id="ebe70-141">Console</span></span>
  * <span data-ttu-id="ebe70-142">Depurar</span><span class="sxs-lookup"><span data-stu-id="ebe70-142">Debug</span></span>
  * <span data-ttu-id="ebe70-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="ebe70-143">EventSource</span></span>
  * <span data-ttu-id="ebe70-144">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="ebe70-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ebe70-145">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ebe70-146">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ebe70-147">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="ebe70-148">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ebe70-149">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ebe70-150">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebe70-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ebe70-151">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="ebe70-152">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="ebe70-152">Enables IIS integration.</span></span> <span data-ttu-id="ebe70-153">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ebe70-154">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="ebe70-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ebe70-155">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="ebe70-155">Framework-provided services</span></span>

<span data-ttu-id="ebe70-156">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="ebe70-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="ebe70-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ebe70-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ebe70-159">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebe70-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ebe70-160">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="ebe70-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ebe70-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="ebe70-162">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ebe70-163">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ebe70-164">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ebe70-165">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="ebe70-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ebe70-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-166">IHostLifetime</span></span>

<span data-ttu-id="ebe70-167">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ebe70-168">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-168">The last implementation registered is used.</span></span>

<span data-ttu-id="ebe70-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="ebe70-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ebe70-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ebe70-171">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="ebe70-172">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ebe70-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ebe70-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebe70-173">IHostEnvironment</span></span>

<span data-ttu-id="ebe70-174">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="ebe70-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="ebe70-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebe70-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ebe70-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebe70-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ebe70-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ebe70-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="ebe70-178">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="ebe70-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ebe70-179">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="ebe70-179">Host configuration</span></span>

<span data-ttu-id="ebe70-180">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ebe70-181">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="ebe70-182">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ebe70-183">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebe70-184">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebe70-185">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ebe70-186">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ebe70-187">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ebe70-188">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ebe70-189">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ebe70-190">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ebe70-191">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ebe70-191">App configuration</span></span>

<span data-ttu-id="ebe70-192">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebe70-193">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebe70-194">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ebe70-195">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="ebe70-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ebe70-196">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ebe70-197">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ebe70-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ebe70-198">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="ebe70-198">Settings for all app types</span></span>

<span data-ttu-id="ebe70-199">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ebe70-200">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="ebe70-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebe70-201">ApplicationName</span></span>

<span data-ttu-id="ebe70-202">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ebe70-203">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="ebe70-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ebe70-204">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-204">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-205">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="ebe70-206">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ebe70-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ebe70-207">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="ebe70-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-208">ContentRoot</span></span>

<span data-ttu-id="ebe70-209">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ebe70-210">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ebe70-211">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebe70-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ebe70-212">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-212">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-213">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="ebe70-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ebe70-214">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ebe70-215">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="ebe70-216">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="ebe70-216">For more information, see:</span></span>

* [<span data-ttu-id="ebe70-217">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="ebe70-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ebe70-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="ebe70-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebe70-219">EnvironmentName</span></span>

<span data-ttu-id="ebe70-220">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ebe70-221">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ebe70-222">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="ebe70-223">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="ebe70-223">**Key**: `environment`</span></span>  
<span data-ttu-id="ebe70-224">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-224">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-225">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="ebe70-225">**Default**: `Production`</span></span>  
<span data-ttu-id="ebe70-226">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ebe70-227">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ebe70-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ebe70-228">ShutdownTimeout</span></span>

<span data-ttu-id="ebe70-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ebe70-230">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-230">The default value is five seconds.</span></span>  <span data-ttu-id="ebe70-231">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="ebe70-232">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ebe70-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ebe70-233">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ebe70-234">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ebe70-235">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ebe70-236">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ebe70-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ebe70-237">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="ebe70-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="ebe70-238">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="ebe70-238">**Type**: `int`</span></span>  
<span data-ttu-id="ebe70-239">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="ebe70-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="ebe70-240">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ebe70-241">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ebe70-242">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="ebe70-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="ebe70-243">Desabilitar recarga de configuração de aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="ebe70-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="ebe70-244">Por [padrão](xref:fundamentals/configuration/index#default), *appsettings.jsem* e *appSettings. { Ambiente}. JSON* são recarregados quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="ebe70-245">Para desabilitar esse comportamento de recarregamento no ASP.NET Core 5,0 ou posterior, defina a `hostBuilder:reloadConfigOnChange` chave como `false` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="ebe70-246">**Chave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ebe70-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="ebe70-247">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-248">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="ebe70-248">**Default**: `true`</span></span>  
<span data-ttu-id="ebe70-249">**Argumento de linha de comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ebe70-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="ebe70-250">**Variável de ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ebe70-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="ebe70-251">O separador de dois-pontos ( `:` ) não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="ebe70-252">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="ebe70-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="ebe70-253">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="ebe70-253">Settings for web apps</span></span>

<span data-ttu-id="ebe70-254">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ebe70-255">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ebe70-256">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="ebe70-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ebe70-257">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ebe70-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ebe70-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ebe70-258">CaptureStartupErrors</span></span>

<span data-ttu-id="ebe70-259">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ebe70-260">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ebe70-261">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ebe70-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="ebe70-262">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-263">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ebe70-264">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ebe70-265">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ebe70-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ebe70-266">DetailedErrors</span></span>

<span data-ttu-id="ebe70-267">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="ebe70-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ebe70-268">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="ebe70-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="ebe70-269">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-270">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="ebe70-270">**Default**: `false`</span></span>  
<span data-ttu-id="ebe70-271">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ebe70-272">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ebe70-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebe70-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="ebe70-274">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="ebe70-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ebe70-275">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ebe70-276">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="ebe70-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ebe70-277">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebe70-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="ebe70-278">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-278">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-279">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="ebe70-279">**Default**: Empty string</span></span>  
<span data-ttu-id="ebe70-280">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebe70-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ebe70-281">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ebe70-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebe70-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ebe70-283">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="ebe70-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ebe70-284">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebe70-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="ebe70-285">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-285">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-286">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="ebe70-286">**Default**: Empty string</span></span>  
<span data-ttu-id="ebe70-287">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebe70-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ebe70-288">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ebe70-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ebe70-289">HTTPS_Port</span></span>

<span data-ttu-id="ebe70-290">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebe70-290">The HTTPS redirect port.</span></span> <span data-ttu-id="ebe70-291">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ebe70-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ebe70-292">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="ebe70-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="ebe70-293">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-293">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-294">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="ebe70-295">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ebe70-296">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ebe70-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ebe70-297">PreferHostingUrls</span></span>

<span data-ttu-id="ebe70-298">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="ebe70-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ebe70-299">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ebe70-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="ebe70-300">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-301">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="ebe70-301">**Default**: `true`</span></span>  
<span data-ttu-id="ebe70-302">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ebe70-303">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ebe70-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ebe70-304">PreventHostingStartup</span></span>

<span data-ttu-id="ebe70-305">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ebe70-306">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ebe70-307">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="ebe70-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="ebe70-308">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-309">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="ebe70-309">**Default**: `false`</span></span>  
<span data-ttu-id="ebe70-310">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ebe70-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ebe70-311">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ebe70-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ebe70-312">StartupAssembly</span></span>

<span data-ttu-id="ebe70-313">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ebe70-314">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="ebe70-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="ebe70-315">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-315">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-316">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ebe70-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ebe70-317">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ebe70-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ebe70-318">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ebe70-319">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="ebe70-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ebe70-320">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="ebe70-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ebe70-321">URLs</span><span class="sxs-lookup"><span data-stu-id="ebe70-321">URLs</span></span>

<span data-ttu-id="ebe70-322">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="ebe70-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ebe70-323">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ebe70-324">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="ebe70-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ebe70-325">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="ebe70-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ebe70-326">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="ebe70-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="ebe70-327">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="ebe70-327">**Key**: `urls`</span></span>  
<span data-ttu-id="ebe70-328">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-328">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-329">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="ebe70-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="ebe70-330">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ebe70-331">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ebe70-332">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="ebe70-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ebe70-333">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ebe70-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-334">WebRoot</span></span>

<span data-ttu-id="ebe70-335">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="ebe70-336">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="ebe70-337">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="ebe70-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="ebe70-338">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-338">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-339">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ebe70-340">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="ebe70-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="ebe70-341">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ebe70-342">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="ebe70-343">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="ebe70-343">For more information, see:</span></span>

* [<span data-ttu-id="ebe70-344">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="ebe70-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ebe70-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ebe70-346">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="ebe70-346">Manage the host lifetime</span></span>

<span data-ttu-id="ebe70-347">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ebe70-348">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="ebe70-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ebe70-349">Executar</span><span class="sxs-lookup"><span data-stu-id="ebe70-349">Run</span></span>

<span data-ttu-id="ebe70-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ebe70-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-351">RunAsync</span></span>

<span data-ttu-id="ebe70-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ebe70-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-353">RunConsoleAsync</span></span>

<span data-ttu-id="ebe70-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ebe70-355">Iniciar</span><span class="sxs-lookup"><span data-stu-id="ebe70-355">Start</span></span>

<span data-ttu-id="ebe70-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="ebe70-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ebe70-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-357">StartAsync</span></span>

<span data-ttu-id="ebe70-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ebe70-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ebe70-360">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ebe70-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-361">StopAsync</span></span>

<span data-ttu-id="ebe70-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ebe70-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ebe70-363">WaitForShutdown</span></span>

<span data-ttu-id="ebe70-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebe70-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ebe70-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="ebe70-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ebe70-367">Controle externo</span><span class="sxs-lookup"><span data-stu-id="ebe70-367">External control</span></span>

<span data-ttu-id="ebe70-368">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="ebe70-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ebe70-369">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="ebe70-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="ebe70-370">Este tópico fornece informações sobre como usar o host genérico .NET no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebe70-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="ebe70-371">Para obter informações sobre como usar o host genérico .NET em aplicativos de console, consulte [host genérico .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="ebe70-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="ebe70-372">Definição de host</span><span class="sxs-lookup"><span data-stu-id="ebe70-372">Host definition</span></span>

<span data-ttu-id="ebe70-373">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="ebe70-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ebe70-374">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="ebe70-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="ebe70-375">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="ebe70-375">Logging</span></span>
* <span data-ttu-id="ebe70-376">Configuração</span><span class="sxs-lookup"><span data-stu-id="ebe70-376">Configuration</span></span>
* <span data-ttu-id="ebe70-377">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="ebe70-377">`IHostedService` implementations</span></span>

<span data-ttu-id="ebe70-378">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="ebe70-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="ebe70-379">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ebe70-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ebe70-380">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ebe70-381">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="ebe70-381">Set up a host</span></span>

<span data-ttu-id="ebe70-382">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ebe70-383">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-383">The `Main` method:</span></span>

* <span data-ttu-id="ebe70-384">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ebe70-385">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ebe70-386">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host genérico:</span><span class="sxs-lookup"><span data-stu-id="ebe70-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="ebe70-387">O código a seguir cria um host genérico usando carga de trabalho não HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="ebe70-388">A `IHostedService` implementação é adicionada ao contêiner di:</span><span class="sxs-lookup"><span data-stu-id="ebe70-388">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="ebe70-389">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ebe70-390">O código anterior é gerado pelos modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebe70-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="ebe70-391">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ebe70-392">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ebe70-393">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ebe70-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ebe70-394">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="ebe70-394">Default builder settings</span></span>

<span data-ttu-id="ebe70-395">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="ebe70-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="ebe70-396">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="ebe70-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="ebe70-397">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="ebe70-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="ebe70-398">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="ebe70-399">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ebe70-399">Command-line arguments.</span></span>
* <span data-ttu-id="ebe70-400">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="ebe70-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="ebe70-401">*appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="ebe70-402">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ebe70-403">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ebe70-404">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-404">Environment variables.</span></span>
  * <span data-ttu-id="ebe70-405">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ebe70-405">Command-line arguments.</span></span>
* <span data-ttu-id="ebe70-406">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="ebe70-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ebe70-407">Console</span><span class="sxs-lookup"><span data-stu-id="ebe70-407">Console</span></span>
  * <span data-ttu-id="ebe70-408">Depurar</span><span class="sxs-lookup"><span data-stu-id="ebe70-408">Debug</span></span>
  * <span data-ttu-id="ebe70-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="ebe70-409">EventSource</span></span>
  * <span data-ttu-id="ebe70-410">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="ebe70-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ebe70-411">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ebe70-412">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ebe70-413">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="ebe70-414">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ebe70-415">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ebe70-416">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebe70-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ebe70-417">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="ebe70-418">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="ebe70-418">Enables IIS integration.</span></span> <span data-ttu-id="ebe70-419">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ebe70-420">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="ebe70-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ebe70-421">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="ebe70-421">Framework-provided services</span></span>

<span data-ttu-id="ebe70-422">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="ebe70-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="ebe70-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ebe70-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ebe70-425">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebe70-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ebe70-426">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="ebe70-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ebe70-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="ebe70-428">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ebe70-429">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ebe70-430">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ebe70-431">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="ebe70-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ebe70-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-432">IHostLifetime</span></span>

<span data-ttu-id="ebe70-433">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ebe70-434">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-434">The last implementation registered is used.</span></span>

<span data-ttu-id="ebe70-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="ebe70-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ebe70-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ebe70-437">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="ebe70-438">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ebe70-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ebe70-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebe70-439">IHostEnvironment</span></span>

<span data-ttu-id="ebe70-440">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="ebe70-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="ebe70-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebe70-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ebe70-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebe70-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ebe70-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ebe70-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="ebe70-444">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="ebe70-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ebe70-445">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="ebe70-445">Host configuration</span></span>

<span data-ttu-id="ebe70-446">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ebe70-447">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="ebe70-448">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ebe70-449">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="ebe70-450">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebe70-451">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ebe70-452">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ebe70-453">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ebe70-454">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ebe70-455">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ebe70-456">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ebe70-457">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ebe70-457">App configuration</span></span>

<span data-ttu-id="ebe70-458">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebe70-459">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebe70-460">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ebe70-461">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="ebe70-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ebe70-462">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ebe70-463">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ebe70-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ebe70-464">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="ebe70-464">Settings for all app types</span></span>

<span data-ttu-id="ebe70-465">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ebe70-466">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="ebe70-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebe70-467">ApplicationName</span></span>

<span data-ttu-id="ebe70-468">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ebe70-469">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="ebe70-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ebe70-470">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-470">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-471">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="ebe70-472">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ebe70-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ebe70-473">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="ebe70-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-474">ContentRoot</span></span>

<span data-ttu-id="ebe70-475">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ebe70-476">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ebe70-477">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebe70-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ebe70-478">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-478">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-479">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="ebe70-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ebe70-480">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ebe70-481">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="ebe70-482">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="ebe70-482">For more information, see:</span></span>

* [<span data-ttu-id="ebe70-483">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="ebe70-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ebe70-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="ebe70-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebe70-485">EnvironmentName</span></span>

<span data-ttu-id="ebe70-486">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ebe70-487">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ebe70-488">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="ebe70-489">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="ebe70-489">**Key**: `environment`</span></span>  
<span data-ttu-id="ebe70-490">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-490">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-491">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="ebe70-491">**Default**: `Production`</span></span>  
<span data-ttu-id="ebe70-492">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ebe70-493">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ebe70-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ebe70-494">ShutdownTimeout</span></span>

<span data-ttu-id="ebe70-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ebe70-496">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-496">The default value is five seconds.</span></span>  <span data-ttu-id="ebe70-497">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="ebe70-498">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ebe70-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ebe70-499">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ebe70-500">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ebe70-501">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ebe70-502">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ebe70-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ebe70-503">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="ebe70-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="ebe70-504">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="ebe70-504">**Type**: `int`</span></span>  
<span data-ttu-id="ebe70-505">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="ebe70-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="ebe70-506">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ebe70-507">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ebe70-508">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="ebe70-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="ebe70-509">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="ebe70-509">Settings for web apps</span></span>

<span data-ttu-id="ebe70-510">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ebe70-511">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ebe70-512">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="ebe70-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ebe70-513">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ebe70-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ebe70-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ebe70-514">CaptureStartupErrors</span></span>

<span data-ttu-id="ebe70-515">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ebe70-516">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ebe70-517">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ebe70-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="ebe70-518">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-519">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ebe70-520">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ebe70-521">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ebe70-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ebe70-522">DetailedErrors</span></span>

<span data-ttu-id="ebe70-523">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="ebe70-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ebe70-524">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="ebe70-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="ebe70-525">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-526">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="ebe70-526">**Default**: `false`</span></span>  
<span data-ttu-id="ebe70-527">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ebe70-528">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ebe70-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebe70-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="ebe70-530">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="ebe70-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ebe70-531">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ebe70-532">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="ebe70-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ebe70-533">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebe70-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="ebe70-534">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-534">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-535">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="ebe70-535">**Default**: Empty string</span></span>  
<span data-ttu-id="ebe70-536">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebe70-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ebe70-537">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ebe70-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebe70-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ebe70-539">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="ebe70-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ebe70-540">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebe70-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="ebe70-541">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-541">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-542">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="ebe70-542">**Default**: Empty string</span></span>  
<span data-ttu-id="ebe70-543">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebe70-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ebe70-544">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ebe70-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ebe70-545">HTTPS_Port</span></span>

<span data-ttu-id="ebe70-546">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebe70-546">The HTTPS redirect port.</span></span> <span data-ttu-id="ebe70-547">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ebe70-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ebe70-548">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="ebe70-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="ebe70-549">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-549">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-550">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="ebe70-551">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ebe70-552">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ebe70-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ebe70-553">PreferHostingUrls</span></span>

<span data-ttu-id="ebe70-554">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="ebe70-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ebe70-555">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ebe70-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="ebe70-556">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-557">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="ebe70-557">**Default**: `true`</span></span>  
<span data-ttu-id="ebe70-558">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ebe70-559">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ebe70-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ebe70-560">PreventHostingStartup</span></span>

<span data-ttu-id="ebe70-561">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ebe70-562">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ebe70-563">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="ebe70-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="ebe70-564">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="ebe70-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebe70-565">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="ebe70-565">**Default**: `false`</span></span>  
<span data-ttu-id="ebe70-566">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ebe70-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ebe70-567">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ebe70-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ebe70-568">StartupAssembly</span></span>

<span data-ttu-id="ebe70-569">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ebe70-570">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="ebe70-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="ebe70-571">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-571">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-572">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ebe70-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ebe70-573">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ebe70-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ebe70-574">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ebe70-575">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="ebe70-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ebe70-576">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="ebe70-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ebe70-577">URLs</span><span class="sxs-lookup"><span data-stu-id="ebe70-577">URLs</span></span>

<span data-ttu-id="ebe70-578">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="ebe70-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ebe70-579">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ebe70-580">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="ebe70-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ebe70-581">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="ebe70-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ebe70-582">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="ebe70-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="ebe70-583">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="ebe70-583">**Key**: `urls`</span></span>  
<span data-ttu-id="ebe70-584">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-584">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-585">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="ebe70-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="ebe70-586">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ebe70-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ebe70-587">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ebe70-588">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="ebe70-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ebe70-589">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ebe70-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-590">WebRoot</span></span>

<span data-ttu-id="ebe70-591">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="ebe70-592">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="ebe70-593">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="ebe70-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="ebe70-594">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-594">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-595">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ebe70-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ebe70-596">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="ebe70-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="ebe70-597">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ebe70-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ebe70-598">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="ebe70-599">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="ebe70-599">For more information, see:</span></span>

* [<span data-ttu-id="ebe70-600">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="ebe70-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ebe70-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebe70-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ebe70-602">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="ebe70-602">Manage the host lifetime</span></span>

<span data-ttu-id="ebe70-603">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ebe70-604">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="ebe70-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ebe70-605">Executar</span><span class="sxs-lookup"><span data-stu-id="ebe70-605">Run</span></span>

<span data-ttu-id="ebe70-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ebe70-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-607">RunAsync</span></span>

<span data-ttu-id="ebe70-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ebe70-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-609">RunConsoleAsync</span></span>

<span data-ttu-id="ebe70-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ebe70-611">Iniciar</span><span class="sxs-lookup"><span data-stu-id="ebe70-611">Start</span></span>

<span data-ttu-id="ebe70-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="ebe70-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ebe70-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-613">StartAsync</span></span>

<span data-ttu-id="ebe70-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ebe70-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ebe70-616">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ebe70-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-617">StopAsync</span></span>

<span data-ttu-id="ebe70-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ebe70-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ebe70-619">WaitForShutdown</span></span>

<span data-ttu-id="ebe70-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebe70-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ebe70-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="ebe70-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ebe70-623">Controle externo</span><span class="sxs-lookup"><span data-stu-id="ebe70-623">External control</span></span>

<span data-ttu-id="ebe70-624">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="ebe70-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ebe70-625">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="ebe70-626">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="ebe70-627">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="ebe70-628">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="ebe70-629">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="ebe70-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="ebe70-630">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="ebe70-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="ebe70-631">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="ebe70-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ebe70-632">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebe70-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="ebe70-633">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebe70-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ebe70-634">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="ebe70-635">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="ebe70-636">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="ebe70-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="ebe70-637">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="ebe70-638">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="ebe70-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="ebe70-639">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="ebe70-640">Introdução</span><span class="sxs-lookup"><span data-stu-id="ebe70-640">Introduction</span></span>

<span data-ttu-id="ebe70-641">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="ebe70-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="ebe70-642">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="ebe70-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="ebe70-643"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="ebe70-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="ebe70-644">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="ebe70-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="ebe70-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ebe70-646">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="ebe70-646">Set up a host</span></span>

<span data-ttu-id="ebe70-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="ebe70-648">Opções</span><span class="sxs-lookup"><span data-stu-id="ebe70-648">Options</span></span>

<span data-ttu-id="ebe70-649">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="ebe70-650">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="ebe70-650">Shutdown timeout</span></span>

<span data-ttu-id="ebe70-651">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ebe70-652">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-652">The default value is five seconds.</span></span>

<span data-ttu-id="ebe70-653">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundo:</span><span class="sxs-lookup"><span data-stu-id="ebe70-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="ebe70-654">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="ebe70-654">Default services</span></span>

<span data-ttu-id="ebe70-655">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="ebe70-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="ebe70-656">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="ebe70-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="ebe70-657">[Configuração](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="ebe70-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="ebe70-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="ebe70-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="ebe70-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="ebe70-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="ebe70-660">[Opções](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="ebe70-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="ebe70-661">[Registro em log](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="ebe70-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ebe70-662">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="ebe70-662">Host configuration</span></span>

<span data-ttu-id="ebe70-663">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="ebe70-663">Host configuration is created by:</span></span>

* <span data-ttu-id="ebe70-664">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="ebe70-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="ebe70-665">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="ebe70-666">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="ebe70-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="ebe70-667">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="ebe70-667">Application key (name)</span></span>

<span data-ttu-id="ebe70-668">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="ebe70-669">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="ebe70-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="ebe70-670">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="ebe70-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ebe70-671">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-671">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-672">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="ebe70-673">**Definir usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="ebe70-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="ebe70-674">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ebe70-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="ebe70-675">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="ebe70-675">Content root</span></span>

<span data-ttu-id="ebe70-676">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="ebe70-677">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebe70-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ebe70-678">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-678">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-679">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="ebe70-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="ebe70-680">**Definir usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebe70-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="ebe70-681">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ebe70-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ebe70-682">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="ebe70-683">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="ebe70-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="ebe70-684">Ambiente</span><span class="sxs-lookup"><span data-stu-id="ebe70-684">Environment</span></span>

<span data-ttu-id="ebe70-685">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ebe70-686">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="ebe70-686">**Key**: `environment`</span></span>  
<span data-ttu-id="ebe70-687">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ebe70-687">**Type**: `string`</span></span>  
<span data-ttu-id="ebe70-688">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="ebe70-688">**Default**: `Production`</span></span>  
<span data-ttu-id="ebe70-689">**Definir usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="ebe70-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="ebe70-690">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ebe70-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ebe70-691">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="ebe70-691">The environment can be set to any value.</span></span> <span data-ttu-id="ebe70-692">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ebe70-693">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="ebe70-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="ebe70-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="ebe70-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="ebe70-696">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="ebe70-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ebe70-698">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ebe70-699">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="ebe70-699">No providers are included by default.</span></span> <span data-ttu-id="ebe70-700">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="ebe70-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="ebe70-701">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="ebe70-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="ebe70-702">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-702">Environment variable configuration.</span></span>
* <span data-ttu-id="ebe70-703">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ebe70-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="ebe70-704">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="ebe70-704">Any other required configuration providers.</span></span>

<span data-ttu-id="ebe70-705">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ebe70-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="ebe70-706">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="ebe70-707">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="ebe70-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="ebe70-708">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="ebe70-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="ebe70-709">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="ebe70-710">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ebe70-711">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="ebe70-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ebe70-712">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ebe70-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="ebe70-713">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="ebe70-714">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ebe70-715">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="ebe70-716">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="ebe70-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="ebe70-717">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ebe70-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="ebe70-718">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="ebe70-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="ebe70-719">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="ebe70-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="ebe70-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ebe70-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ebe70-721">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="ebe70-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="ebe70-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ebe70-724">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="ebe70-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="ebe70-725">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="ebe70-726">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ebe70-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="ebe70-727">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="ebe70-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="ebe70-728">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="ebe70-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="ebe70-729">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="ebe70-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="ebe70-730">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="ebe70-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="ebe70-731">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="ebe70-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="ebe70-732">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="ebe70-733">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="ebe70-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="ebe70-734">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="ebe70-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="ebe70-735">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="ebe70-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ebe70-736">ConfigureServices</span></span>

<span data-ttu-id="ebe70-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ebe70-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ebe70-739">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="ebe70-740">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="ebe70-741">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ebe70-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="ebe70-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="ebe70-742">ConfigureLogging</span></span>

<span data-ttu-id="ebe70-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="ebe70-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="ebe70-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-745">UseConsoleLifetime</span></span>

<span data-ttu-id="ebe70-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="ebe70-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ebe70-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="ebe70-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="ebe70-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="ebe70-749">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="ebe70-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="ebe70-750">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="ebe70-750">Container configuration</span></span>

<span data-ttu-id="ebe70-751">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="ebe70-752">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="ebe70-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="ebe70-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="ebe70-754">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="ebe70-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="ebe70-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ebe70-757">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ebe70-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="ebe70-758">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="ebe70-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="ebe70-759">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ebe70-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="ebe70-760">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="ebe70-760">Extensibility</span></span>

<span data-ttu-id="ebe70-761">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="ebe70-762">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="ebe70-763">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="ebe70-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="ebe70-764">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="ebe70-764">Manage the host</span></span>

<span data-ttu-id="ebe70-765">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="ebe70-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ebe70-766">Executar</span><span class="sxs-lookup"><span data-stu-id="ebe70-766">Run</span></span>

<span data-ttu-id="ebe70-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="ebe70-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="ebe70-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-768">RunAsync</span></span>

<span data-ttu-id="ebe70-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="ebe70-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="ebe70-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-770">RunConsoleAsync</span></span>

<span data-ttu-id="ebe70-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="ebe70-772">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-772">Start and StopAsync</span></span>

<span data-ttu-id="ebe70-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="ebe70-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="ebe70-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="ebe70-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="ebe70-775">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="ebe70-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="ebe70-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="ebe70-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ebe70-778">WaitForShutdown</span></span>

<span data-ttu-id="ebe70-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é disparado por meio do <xref:Microsoft.Extensions.Hosting.IHostLifetime> , como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta para <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ebe70-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="ebe70-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="ebe70-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ebe70-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="ebe70-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="ebe70-783">Controle externo</span><span class="sxs-lookup"><span data-stu-id="ebe70-783">External control</span></span>

<span data-ttu-id="ebe70-784">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="ebe70-784">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="ebe70-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ebe70-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ebe70-786">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="ebe70-787">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebe70-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="ebe70-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="ebe70-789">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="ebe70-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="ebe70-790">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ebe70-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="ebe70-791">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebe70-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="ebe70-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="ebe70-793">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="ebe70-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ebe70-794">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="ebe70-794">Cancellation Token</span></span> | <span data-ttu-id="ebe70-795">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="ebe70-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="ebe70-796">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="ebe70-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="ebe70-797">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ebe70-798">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-798">All requests should be processed.</span></span> <span data-ttu-id="ebe70-799">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="ebe70-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="ebe70-800">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="ebe70-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ebe70-801">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="ebe70-801">Requests may still be processing.</span></span> <span data-ttu-id="ebe70-802">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="ebe70-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="ebe70-803">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="ebe70-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="ebe70-804">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="ebe70-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="ebe70-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="ebe70-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="ebe70-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ebe70-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="ebe70-807">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="ebe70-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ebe70-808">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ebe70-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
