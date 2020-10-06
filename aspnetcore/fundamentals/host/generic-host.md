---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
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
ms.openlocfilehash: 59cfae9ff619f8de894686c4b773d66e5cbe10ad
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754730"
---
# <a name="net-generic-host"></a><span data-ttu-id="5117a-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="5117a-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5117a-104">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="5117a-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="5117a-105">Definição de host</span><span class="sxs-lookup"><span data-stu-id="5117a-105">Host definition</span></span>

<span data-ttu-id="5117a-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="5117a-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5117a-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="5117a-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="5117a-108">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="5117a-108">Logging</span></span>
* <span data-ttu-id="5117a-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="5117a-109">Configuration</span></span>
* <span data-ttu-id="5117a-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="5117a-110">`IHostedService` implementations</span></span>

<span data-ttu-id="5117a-111">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="5117a-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="5117a-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="5117a-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5117a-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5117a-114">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="5117a-114">Set up a host</span></span>

<span data-ttu-id="5117a-115">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="5117a-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5117a-116">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="5117a-116">The `Main` method:</span></span>

* <span data-ttu-id="5117a-117">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="5117a-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5117a-118">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="5117a-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5117a-119">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="5117a-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="5117a-120">O código a seguir cria uma carga de trabalho não HTTP com uma `IHostedService` implementação adicionada ao contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="5117a-120">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="5117a-121">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="5117a-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5117a-122">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5117a-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5117a-123">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5117a-124">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="5117a-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5117a-125">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="5117a-125">Default builder settings</span></span>

<span data-ttu-id="5117a-126">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="5117a-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="5117a-127">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="5117a-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="5117a-128">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="5117a-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="5117a-129">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="5117a-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5117a-130">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5117a-130">Command-line arguments.</span></span>
* <span data-ttu-id="5117a-131">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="5117a-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="5117a-132">*appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="5117a-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="5117a-133">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5117a-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5117a-134">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="5117a-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5117a-135">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5117a-135">Environment variables.</span></span>
  * <span data-ttu-id="5117a-136">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5117a-136">Command-line arguments.</span></span>
* <span data-ttu-id="5117a-137">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="5117a-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5117a-138">Console</span><span class="sxs-lookup"><span data-stu-id="5117a-138">Console</span></span>
  * <span data-ttu-id="5117a-139">Depurar</span><span class="sxs-lookup"><span data-stu-id="5117a-139">Debug</span></span>
  * <span data-ttu-id="5117a-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="5117a-140">EventSource</span></span>
  * <span data-ttu-id="5117a-141">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="5117a-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5117a-142">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5117a-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5117a-143">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="5117a-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5117a-144">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="5117a-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5117a-145">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5117a-146">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="5117a-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5117a-147">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="5117a-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5117a-148">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="5117a-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5117a-149">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="5117a-149">Enables IIS integration.</span></span> <span data-ttu-id="5117a-150">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="5117a-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5117a-151">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="5117a-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5117a-152">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="5117a-152">Framework-provided services</span></span>

<span data-ttu-id="5117a-153">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="5117a-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5117a-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5117a-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5117a-156">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5117a-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5117a-157">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="5117a-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5117a-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="5117a-159">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5117a-160">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5117a-161">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="5117a-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5117a-162">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="5117a-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5117a-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-163">IHostLifetime</span></span>

<span data-ttu-id="5117a-164">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="5117a-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5117a-165">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="5117a-165">The last implementation registered is used.</span></span>

<span data-ttu-id="5117a-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="5117a-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5117a-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5117a-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5117a-168">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="5117a-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="5117a-169">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="5117a-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5117a-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5117a-170">IHostEnvironment</span></span>

<span data-ttu-id="5117a-171">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="5117a-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5117a-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5117a-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5117a-173">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5117a-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5117a-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="5117a-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="5117a-175">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="5117a-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5117a-176">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="5117a-176">Host configuration</span></span>

<span data-ttu-id="5117a-177">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="5117a-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5117a-178">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="5117a-179">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5117a-180">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5117a-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5117a-181">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5117a-182">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="5117a-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5117a-183">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5117a-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5117a-184">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="5117a-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5117a-185">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="5117a-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5117a-186">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="5117a-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5117a-187">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="5117a-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5117a-188">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5117a-188">App configuration</span></span>

<span data-ttu-id="5117a-189">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5117a-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5117a-190">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5117a-191">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="5117a-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5117a-192">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="5117a-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5117a-193">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5117a-194">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5117a-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5117a-195">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="5117a-195">Settings for all app types</span></span>

<span data-ttu-id="5117a-196">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5117a-197">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="5117a-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5117a-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5117a-198">ApplicationName</span></span>

<span data-ttu-id="5117a-199">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="5117a-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5117a-200">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="5117a-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5117a-201">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-201">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-202">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5117a-203">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5117a-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5117a-204">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5117a-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="5117a-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-205">ContentRoot</span></span>

<span data-ttu-id="5117a-206">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="5117a-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5117a-207">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="5117a-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5117a-208">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5117a-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5117a-209">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-209">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-210">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="5117a-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5117a-211">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5117a-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5117a-212">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5117a-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5117a-213">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="5117a-213">For more information, see:</span></span>

* [<span data-ttu-id="5117a-214">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="5117a-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5117a-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5117a-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5117a-216">EnvironmentName</span></span>

<span data-ttu-id="5117a-217">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="5117a-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5117a-218">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="5117a-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5117a-219">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5117a-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5117a-220">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="5117a-220">**Key**: `environment`</span></span>  
<span data-ttu-id="5117a-221">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-221">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-222">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="5117a-222">**Default**: `Production`</span></span>  
<span data-ttu-id="5117a-223">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5117a-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5117a-224">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5117a-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5117a-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="5117a-225">ShutdownTimeout</span></span>

<span data-ttu-id="5117a-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5117a-227">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="5117a-227">The default value is five seconds.</span></span>  <span data-ttu-id="5117a-228">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="5117a-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="5117a-229">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5117a-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5117a-230">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="5117a-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5117a-231">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="5117a-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5117a-232">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="5117a-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5117a-233">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="5117a-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5117a-234">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5117a-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5117a-235">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="5117a-235">**Type**: `int`</span></span>  
<span data-ttu-id="5117a-236">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="5117a-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5117a-237">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5117a-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5117a-238">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="5117a-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5117a-239">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="5117a-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="5117a-240">Desabilitar recarga de configuração de aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="5117a-240">Disable app configuration reload on change</span></span>

<span data-ttu-id="5117a-241">Por [padrão](xref:fundamentals/configuration/index#default), *appsettings.jsem* e *appSettings. { Ambiente}. JSON* são recarregados quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="5117a-241">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="5117a-242">Para desabilitar esse comportamento de recarregamento no ASP.NET Core 5,0 ou posterior, defina a `hostBuilder:reloadConfigOnChange` chave como `false` .</span><span class="sxs-lookup"><span data-stu-id="5117a-242">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="5117a-243">**Chave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5117a-243">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5117a-244">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-244">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-245">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="5117a-245">**Default**: `true`</span></span>  
<span data-ttu-id="5117a-246">**Argumento de linha de comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5117a-246">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5117a-247">**Variável de ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5117a-247">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="5117a-248">O separador de dois-pontos ( `:` ) não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="5117a-248">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="5117a-249">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="5117a-249">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="5117a-250">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="5117a-250">Settings for web apps</span></span>

<span data-ttu-id="5117a-251">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-251">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5117a-252">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="5117a-252">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5117a-253">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="5117a-253">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5117a-254">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5117a-254">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5117a-255">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="5117a-255">CaptureStartupErrors</span></span>

<span data-ttu-id="5117a-256">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="5117a-256">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5117a-257">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="5117a-257">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5117a-258">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5117a-258">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5117a-259">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-259">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-260">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="5117a-260">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5117a-261">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5117a-261">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5117a-262">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="5117a-262">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5117a-263">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="5117a-263">DetailedErrors</span></span>

<span data-ttu-id="5117a-264">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="5117a-264">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5117a-265">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5117a-265">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5117a-266">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-266">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-267">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="5117a-267">**Default**: `false`</span></span>  
<span data-ttu-id="5117a-268">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5117a-268">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5117a-269">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5117a-270">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5117a-270">HostingStartupAssemblies</span></span>

<span data-ttu-id="5117a-271">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="5117a-271">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5117a-272">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-272">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5117a-273">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="5117a-273">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5117a-274">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5117a-274">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5117a-275">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-275">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-276">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="5117a-276">**Default**: Empty string</span></span>  
<span data-ttu-id="5117a-277">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5117a-277">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5117a-278">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-278">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5117a-279">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="5117a-279">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5117a-280">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="5117a-280">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5117a-281">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5117a-281">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5117a-282">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-282">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-283">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="5117a-283">**Default**: Empty string</span></span>  
<span data-ttu-id="5117a-284">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5117a-284">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5117a-285">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5117a-286">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5117a-286">HTTPS_Port</span></span>

<span data-ttu-id="5117a-287">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5117a-287">The HTTPS redirect port.</span></span> <span data-ttu-id="5117a-288">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5117a-288">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5117a-289">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="5117a-289">**Key**: `https_port`</span></span>  
<span data-ttu-id="5117a-290">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-290">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-291">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="5117a-291">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5117a-292">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5117a-292">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5117a-293">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-293">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5117a-294">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5117a-294">PreferHostingUrls</span></span>

<span data-ttu-id="5117a-295">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="5117a-295">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5117a-296">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5117a-296">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5117a-297">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-298">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="5117a-298">**Default**: `true`</span></span>  
<span data-ttu-id="5117a-299">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5117a-299">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5117a-300">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="5117a-300">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5117a-301">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="5117a-301">PreventHostingStartup</span></span>

<span data-ttu-id="5117a-302">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-302">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5117a-303">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5117a-303">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5117a-304">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5117a-304">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5117a-305">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-305">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-306">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="5117a-306">**Default**: `false`</span></span>  
<span data-ttu-id="5117a-307">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5117a-307">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5117a-308">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-308">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5117a-309">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="5117a-309">StartupAssembly</span></span>

<span data-ttu-id="5117a-310">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="5117a-310">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5117a-311">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5117a-311">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5117a-312">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-312">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-313">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5117a-313">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5117a-314">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5117a-314">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5117a-315">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="5117a-315">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5117a-316">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="5117a-316">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5117a-317">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="5117a-317">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5117a-318">URLs</span><span class="sxs-lookup"><span data-stu-id="5117a-318">URLs</span></span>

<span data-ttu-id="5117a-319">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="5117a-319">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5117a-320">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5117a-320">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5117a-321">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="5117a-321">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5117a-322">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="5117a-322">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5117a-323">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="5117a-323">Supported formats vary among servers.</span></span>

<span data-ttu-id="5117a-324">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="5117a-324">**Key**: `urls`</span></span>  
<span data-ttu-id="5117a-325">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-325">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-326">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5117a-326">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5117a-327">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5117a-327">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5117a-328">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="5117a-328">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5117a-329">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5117a-329">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5117a-330">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5117a-330">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5117a-331">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-331">WebRoot</span></span>

<span data-ttu-id="5117a-332">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-332">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="5117a-333">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="5117a-333">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="5117a-334">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="5117a-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="5117a-335">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-335">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-336">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="5117a-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5117a-337">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="5117a-337">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="5117a-338">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5117a-338">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5117a-339">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5117a-339">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5117a-340">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="5117a-340">For more information, see:</span></span>

* [<span data-ttu-id="5117a-341">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="5117a-341">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5117a-342">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-342">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5117a-343">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="5117a-343">Manage the host lifetime</span></span>

<span data-ttu-id="5117a-344">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-344">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5117a-345">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="5117a-345">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5117a-346">Executar</span><span class="sxs-lookup"><span data-stu-id="5117a-346">Run</span></span>

<span data-ttu-id="5117a-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="5117a-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5117a-348">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-348">RunAsync</span></span>

<span data-ttu-id="5117a-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="5117a-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5117a-350">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-350">RunConsoleAsync</span></span>

<span data-ttu-id="5117a-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="5117a-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5117a-352">Iniciar</span><span class="sxs-lookup"><span data-stu-id="5117a-352">Start</span></span>

<span data-ttu-id="5117a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="5117a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5117a-354">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-354">StartAsync</span></span>

<span data-ttu-id="5117a-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="5117a-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5117a-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="5117a-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5117a-357">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="5117a-357">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5117a-358">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-358">StopAsync</span></span>

<span data-ttu-id="5117a-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="5117a-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5117a-360">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="5117a-360">WaitForShutdown</span></span>

<span data-ttu-id="5117a-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5117a-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5117a-362">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-362">WaitForShutdownAsync</span></span>

<span data-ttu-id="5117a-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5117a-364">Controle externo</span><span class="sxs-lookup"><span data-stu-id="5117a-364">External control</span></span>

<span data-ttu-id="5117a-365">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="5117a-365">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5117a-366">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="5117a-366">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="5117a-367">Definição de host</span><span class="sxs-lookup"><span data-stu-id="5117a-367">Host definition</span></span>

<span data-ttu-id="5117a-368">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="5117a-368">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5117a-369">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="5117a-369">Dependency injection (DI)</span></span>
* <span data-ttu-id="5117a-370">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="5117a-370">Logging</span></span>
* <span data-ttu-id="5117a-371">Configuração</span><span class="sxs-lookup"><span data-stu-id="5117a-371">Configuration</span></span>
* <span data-ttu-id="5117a-372">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="5117a-372">`IHostedService` implementations</span></span>

<span data-ttu-id="5117a-373">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="5117a-373">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="5117a-374">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="5117a-374">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5117a-375">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-375">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5117a-376">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="5117a-376">Set up a host</span></span>

<span data-ttu-id="5117a-377">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="5117a-377">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5117a-378">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="5117a-378">The `Main` method:</span></span>

* <span data-ttu-id="5117a-379">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="5117a-379">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5117a-380">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="5117a-380">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5117a-381">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host genérico:</span><span class="sxs-lookup"><span data-stu-id="5117a-381">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="5117a-382">O código a seguir cria um host genérico usando carga de trabalho não HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-382">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="5117a-383">A `IHostedService` implementação é adicionada ao contêiner di:</span><span class="sxs-lookup"><span data-stu-id="5117a-383">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="5117a-384">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="5117a-384">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5117a-385">O código anterior é gerado pelos modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5117a-385">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="5117a-386">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5117a-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5117a-387">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5117a-388">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="5117a-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5117a-389">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="5117a-389">Default builder settings</span></span>

<span data-ttu-id="5117a-390">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="5117a-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="5117a-391">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="5117a-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="5117a-392">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="5117a-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="5117a-393">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="5117a-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5117a-394">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5117a-394">Command-line arguments.</span></span>
* <span data-ttu-id="5117a-395">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="5117a-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="5117a-396">*appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="5117a-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="5117a-397">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5117a-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5117a-398">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="5117a-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5117a-399">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5117a-399">Environment variables.</span></span>
  * <span data-ttu-id="5117a-400">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5117a-400">Command-line arguments.</span></span>
* <span data-ttu-id="5117a-401">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="5117a-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5117a-402">Console</span><span class="sxs-lookup"><span data-stu-id="5117a-402">Console</span></span>
  * <span data-ttu-id="5117a-403">Depurar</span><span class="sxs-lookup"><span data-stu-id="5117a-403">Debug</span></span>
  * <span data-ttu-id="5117a-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="5117a-404">EventSource</span></span>
  * <span data-ttu-id="5117a-405">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="5117a-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5117a-406">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5117a-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5117a-407">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="5117a-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5117a-408">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="5117a-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5117a-409">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5117a-410">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="5117a-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5117a-411">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="5117a-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5117a-412">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="5117a-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5117a-413">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="5117a-413">Enables IIS integration.</span></span> <span data-ttu-id="5117a-414">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="5117a-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5117a-415">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="5117a-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5117a-416">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="5117a-416">Framework-provided services</span></span>

<span data-ttu-id="5117a-417">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="5117a-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5117a-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5117a-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5117a-420">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5117a-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5117a-421">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="5117a-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5117a-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="5117a-423">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5117a-424">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5117a-425">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="5117a-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5117a-426">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="5117a-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5117a-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-427">IHostLifetime</span></span>

<span data-ttu-id="5117a-428">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="5117a-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5117a-429">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="5117a-429">The last implementation registered is used.</span></span>

<span data-ttu-id="5117a-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="5117a-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5117a-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5117a-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5117a-432">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="5117a-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="5117a-433">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="5117a-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5117a-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5117a-434">IHostEnvironment</span></span>

<span data-ttu-id="5117a-435">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="5117a-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5117a-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5117a-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5117a-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5117a-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5117a-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="5117a-438">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="5117a-439">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="5117a-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5117a-440">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="5117a-440">Host configuration</span></span>

<span data-ttu-id="5117a-441">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="5117a-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5117a-442">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="5117a-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="5117a-443">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5117a-444">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5117a-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="5117a-445">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5117a-446">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="5117a-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5117a-447">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5117a-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5117a-448">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="5117a-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5117a-449">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="5117a-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5117a-450">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="5117a-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5117a-451">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="5117a-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5117a-452">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5117a-452">App configuration</span></span>

<span data-ttu-id="5117a-453">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5117a-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5117a-454">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5117a-455">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="5117a-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5117a-456">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="5117a-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5117a-457">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5117a-458">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5117a-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5117a-459">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="5117a-459">Settings for all app types</span></span>

<span data-ttu-id="5117a-460">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5117a-461">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="5117a-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5117a-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5117a-462">ApplicationName</span></span>

<span data-ttu-id="5117a-463">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="5117a-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5117a-464">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="5117a-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5117a-465">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-465">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-466">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5117a-467">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5117a-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5117a-468">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5117a-468">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="5117a-469">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-469">ContentRoot</span></span>

<span data-ttu-id="5117a-470">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="5117a-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5117a-471">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="5117a-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5117a-472">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5117a-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5117a-473">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-473">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-474">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="5117a-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5117a-475">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5117a-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5117a-476">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5117a-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5117a-477">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="5117a-477">For more information, see:</span></span>

* [<span data-ttu-id="5117a-478">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="5117a-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5117a-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5117a-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5117a-480">EnvironmentName</span></span>

<span data-ttu-id="5117a-481">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="5117a-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5117a-482">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="5117a-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5117a-483">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5117a-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5117a-484">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="5117a-484">**Key**: `environment`</span></span>  
<span data-ttu-id="5117a-485">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-485">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-486">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="5117a-486">**Default**: `Production`</span></span>  
<span data-ttu-id="5117a-487">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5117a-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5117a-488">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5117a-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5117a-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="5117a-489">ShutdownTimeout</span></span>

<span data-ttu-id="5117a-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5117a-491">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="5117a-491">The default value is five seconds.</span></span>  <span data-ttu-id="5117a-492">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="5117a-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="5117a-493">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5117a-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5117a-494">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="5117a-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5117a-495">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="5117a-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5117a-496">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="5117a-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5117a-497">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="5117a-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5117a-498">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5117a-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5117a-499">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="5117a-499">**Type**: `int`</span></span>  
<span data-ttu-id="5117a-500">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="5117a-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5117a-501">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5117a-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5117a-502">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="5117a-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5117a-503">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="5117a-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="5117a-504">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="5117a-504">Settings for web apps</span></span>

<span data-ttu-id="5117a-505">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5117a-506">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="5117a-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5117a-507">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="5117a-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5117a-508">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5117a-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5117a-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="5117a-509">CaptureStartupErrors</span></span>

<span data-ttu-id="5117a-510">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="5117a-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5117a-511">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="5117a-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5117a-512">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5117a-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5117a-513">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-514">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="5117a-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5117a-515">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5117a-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5117a-516">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="5117a-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5117a-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="5117a-517">DetailedErrors</span></span>

<span data-ttu-id="5117a-518">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="5117a-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5117a-519">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5117a-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5117a-520">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-521">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="5117a-521">**Default**: `false`</span></span>  
<span data-ttu-id="5117a-522">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5117a-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5117a-523">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5117a-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5117a-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="5117a-525">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="5117a-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5117a-526">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5117a-527">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="5117a-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5117a-528">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5117a-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5117a-529">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-529">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-530">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="5117a-530">**Default**: Empty string</span></span>  
<span data-ttu-id="5117a-531">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5117a-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5117a-532">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5117a-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="5117a-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5117a-534">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="5117a-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5117a-535">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5117a-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5117a-536">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-536">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-537">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="5117a-537">**Default**: Empty string</span></span>  
<span data-ttu-id="5117a-538">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5117a-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5117a-539">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5117a-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5117a-540">HTTPS_Port</span></span>

<span data-ttu-id="5117a-541">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5117a-541">The HTTPS redirect port.</span></span> <span data-ttu-id="5117a-542">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5117a-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5117a-543">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="5117a-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="5117a-544">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-544">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-545">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="5117a-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5117a-546">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5117a-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5117a-547">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5117a-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5117a-548">PreferHostingUrls</span></span>

<span data-ttu-id="5117a-549">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="5117a-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5117a-550">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5117a-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5117a-551">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-552">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="5117a-552">**Default**: `true`</span></span>  
<span data-ttu-id="5117a-553">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5117a-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5117a-554">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="5117a-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5117a-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="5117a-555">PreventHostingStartup</span></span>

<span data-ttu-id="5117a-556">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5117a-557">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5117a-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5117a-558">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5117a-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5117a-559">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="5117a-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5117a-560">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="5117a-560">**Default**: `false`</span></span>  
<span data-ttu-id="5117a-561">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5117a-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5117a-562">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="5117a-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5117a-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="5117a-563">StartupAssembly</span></span>

<span data-ttu-id="5117a-564">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="5117a-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5117a-565">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5117a-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5117a-566">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-566">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-567">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5117a-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5117a-568">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5117a-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5117a-569">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="5117a-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5117a-570">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="5117a-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5117a-571">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="5117a-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5117a-572">URLs</span><span class="sxs-lookup"><span data-stu-id="5117a-572">URLs</span></span>

<span data-ttu-id="5117a-573">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="5117a-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5117a-574">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5117a-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5117a-575">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="5117a-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5117a-576">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="5117a-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5117a-577">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="5117a-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="5117a-578">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="5117a-578">**Key**: `urls`</span></span>  
<span data-ttu-id="5117a-579">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-579">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-580">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5117a-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5117a-581">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5117a-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5117a-582">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="5117a-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5117a-583">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5117a-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5117a-584">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5117a-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5117a-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-585">WebRoot</span></span>

<span data-ttu-id="5117a-586">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-586">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="5117a-587">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="5117a-587">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="5117a-588">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="5117a-588">**Key**: `webroot`</span></span>  
<span data-ttu-id="5117a-589">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-589">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-590">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="5117a-590">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5117a-591">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="5117a-591">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="5117a-592">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5117a-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5117a-593">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5117a-593">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5117a-594">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="5117a-594">For more information, see:</span></span>

* [<span data-ttu-id="5117a-595">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="5117a-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5117a-596">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5117a-596">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5117a-597">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="5117a-597">Manage the host lifetime</span></span>

<span data-ttu-id="5117a-598">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5117a-599">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="5117a-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5117a-600">Executar</span><span class="sxs-lookup"><span data-stu-id="5117a-600">Run</span></span>

<span data-ttu-id="5117a-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="5117a-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5117a-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-602">RunAsync</span></span>

<span data-ttu-id="5117a-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="5117a-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5117a-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-604">RunConsoleAsync</span></span>

<span data-ttu-id="5117a-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="5117a-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5117a-606">Iniciar</span><span class="sxs-lookup"><span data-stu-id="5117a-606">Start</span></span>

<span data-ttu-id="5117a-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="5117a-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5117a-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-608">StartAsync</span></span>

<span data-ttu-id="5117a-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="5117a-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5117a-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="5117a-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5117a-611">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="5117a-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5117a-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-612">StopAsync</span></span>

<span data-ttu-id="5117a-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="5117a-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5117a-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="5117a-614">WaitForShutdown</span></span>

<span data-ttu-id="5117a-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5117a-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5117a-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="5117a-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5117a-618">Controle externo</span><span class="sxs-lookup"><span data-stu-id="5117a-618">External control</span></span>

<span data-ttu-id="5117a-619">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="5117a-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5117a-620">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="5117a-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="5117a-621">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="5117a-622">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="5117a-623">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="5117a-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="5117a-624">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="5117a-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="5117a-625">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="5117a-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="5117a-626">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="5117a-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5117a-627">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="5117a-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="5117a-628">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5117a-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5117a-629">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="5117a-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="5117a-630">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="5117a-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="5117a-631">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="5117a-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="5117a-632">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="5117a-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="5117a-633">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="5117a-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="5117a-634">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="5117a-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="5117a-635">Introdução</span><span class="sxs-lookup"><span data-stu-id="5117a-635">Introduction</span></span>

<span data-ttu-id="5117a-636">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="5117a-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="5117a-637">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="5117a-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="5117a-638"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="5117a-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="5117a-639">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="5117a-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="5117a-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="5117a-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5117a-641">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="5117a-641">Set up a host</span></span>

<span data-ttu-id="5117a-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="5117a-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="5117a-643">Opções</span><span class="sxs-lookup"><span data-stu-id="5117a-643">Options</span></span>

<span data-ttu-id="5117a-644">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="5117a-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="5117a-645">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="5117a-645">Shutdown timeout</span></span>

<span data-ttu-id="5117a-646">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5117a-647">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="5117a-647">The default value is five seconds.</span></span>

<span data-ttu-id="5117a-648">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundo:</span><span class="sxs-lookup"><span data-stu-id="5117a-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="5117a-649">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="5117a-649">Default services</span></span>

<span data-ttu-id="5117a-650">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="5117a-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="5117a-651">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="5117a-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="5117a-652">[Configuração](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="5117a-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="5117a-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5117a-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="5117a-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5117a-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="5117a-655">[Opções](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="5117a-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="5117a-656">[Registro em log](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="5117a-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5117a-657">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="5117a-657">Host configuration</span></span>

<span data-ttu-id="5117a-658">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="5117a-658">Host configuration is created by:</span></span>

* <span data-ttu-id="5117a-659">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="5117a-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="5117a-660">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="5117a-661">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="5117a-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="5117a-662">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="5117a-662">Application key (name)</span></span>

<span data-ttu-id="5117a-663">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="5117a-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="5117a-664">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="5117a-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="5117a-665">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="5117a-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5117a-666">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-666">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-667">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="5117a-668">**Definir usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="5117a-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="5117a-669">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5117a-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="5117a-670">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="5117a-670">Content root</span></span>

<span data-ttu-id="5117a-671">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="5117a-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="5117a-672">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5117a-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5117a-673">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-673">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-674">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="5117a-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="5117a-675">**Definir usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="5117a-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="5117a-676">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5117a-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5117a-677">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="5117a-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="5117a-678">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="5117a-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="5117a-679">Ambiente</span><span class="sxs-lookup"><span data-stu-id="5117a-679">Environment</span></span>

<span data-ttu-id="5117a-680">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5117a-681">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="5117a-681">**Key**: `environment`</span></span>  
<span data-ttu-id="5117a-682">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="5117a-682">**Type**: `string`</span></span>  
<span data-ttu-id="5117a-683">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="5117a-683">**Default**: `Production`</span></span>  
<span data-ttu-id="5117a-684">**Definir usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="5117a-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="5117a-685">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5117a-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5117a-686">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="5117a-686">The environment can be set to any value.</span></span> <span data-ttu-id="5117a-687">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="5117a-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5117a-688">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5117a-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="5117a-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="5117a-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="5117a-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="5117a-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="5117a-691">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="5117a-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5117a-693">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="5117a-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5117a-694">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="5117a-694">No providers are included by default.</span></span> <span data-ttu-id="5117a-695">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="5117a-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="5117a-696">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="5117a-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="5117a-697">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5117a-697">Environment variable configuration.</span></span>
* <span data-ttu-id="5117a-698">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5117a-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="5117a-699">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="5117a-699">Any other required configuration providers.</span></span>

<span data-ttu-id="5117a-700">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5117a-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="5117a-701">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5117a-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="5117a-702">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="5117a-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="5117a-703">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="5117a-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="5117a-704">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="5117a-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="5117a-705">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="5117a-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5117a-706">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="5117a-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5117a-707">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5117a-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="5117a-708">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5117a-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="5117a-709">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5117a-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5117a-710">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="5117a-711">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="5117a-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="5117a-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5117a-712">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="5117a-713">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="5117a-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="5117a-714">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="5117a-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="5117a-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5117a-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5117a-716">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5117a-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="5117a-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="5117a-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5117a-719">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="5117a-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="5117a-720">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="5117a-721">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5117a-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="5117a-722">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="5117a-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="5117a-723">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="5117a-723">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="5117a-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="5117a-724">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="5117a-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="5117a-725">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="5117a-726">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="5117a-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="5117a-727">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="5117a-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="5117a-728">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="5117a-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="5117a-729">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="5117a-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="5117a-730">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5117a-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="5117a-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="5117a-731">ConfigureServices</span></span>

<span data-ttu-id="5117a-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5117a-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5117a-734">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="5117a-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="5117a-735">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="5117a-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="5117a-736">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5117a-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="5117a-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="5117a-737">ConfigureLogging</span></span>

<span data-ttu-id="5117a-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="5117a-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="5117a-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="5117a-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-740">UseConsoleLifetime</span></span>

<span data-ttu-id="5117a-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="5117a-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="5117a-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="5117a-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="5117a-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="5117a-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="5117a-744">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="5117a-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="5117a-745">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="5117a-745">Container configuration</span></span>

<span data-ttu-id="5117a-746">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="5117a-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="5117a-747">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="5117a-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="5117a-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="5117a-749">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="5117a-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="5117a-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="5117a-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="5117a-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5117a-752">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5117a-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="5117a-753">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="5117a-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="5117a-754">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5117a-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="5117a-755">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="5117a-755">Extensibility</span></span>

<span data-ttu-id="5117a-756">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5117a-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="5117a-757">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="5117a-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="5117a-758">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="5117a-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="5117a-759">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="5117a-759">Manage the host</span></span>

<span data-ttu-id="5117a-760">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="5117a-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5117a-761">Executar</span><span class="sxs-lookup"><span data-stu-id="5117a-761">Run</span></span>

<span data-ttu-id="5117a-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="5117a-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="5117a-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-763">RunAsync</span></span>

<span data-ttu-id="5117a-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="5117a-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="5117a-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-765">RunConsoleAsync</span></span>

<span data-ttu-id="5117a-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="5117a-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="5117a-767">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-767">Start and StopAsync</span></span>

<span data-ttu-id="5117a-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="5117a-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="5117a-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="5117a-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="5117a-770">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="5117a-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="5117a-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="5117a-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="5117a-773">WaitForShutdown</span></span>

<span data-ttu-id="5117a-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é disparado por meio do <xref:Microsoft.Extensions.Hosting.IHostLifetime> , como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta para <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="5117a-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="5117a-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="5117a-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5117a-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="5117a-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="5117a-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="5117a-778">Controle externo</span><span class="sxs-lookup"><span data-stu-id="5117a-778">External control</span></span>

<span data-ttu-id="5117a-779">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="5117a-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="5117a-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="5117a-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5117a-781">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="5117a-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="5117a-782">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="5117a-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="5117a-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="5117a-784">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="5117a-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="5117a-785">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5117a-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="5117a-786">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5117a-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="5117a-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="5117a-788">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="5117a-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="5117a-789">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="5117a-789">Cancellation Token</span></span> | <span data-ttu-id="5117a-790">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="5117a-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="5117a-791">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="5117a-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="5117a-792">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="5117a-793">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="5117a-793">All requests should be processed.</span></span> <span data-ttu-id="5117a-794">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="5117a-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="5117a-795">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="5117a-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="5117a-796">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="5117a-796">Requests may still be processing.</span></span> <span data-ttu-id="5117a-797">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="5117a-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="5117a-798">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="5117a-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="5117a-799">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="5117a-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="5117a-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5117a-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="5117a-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5117a-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="5117a-802">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="5117a-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5117a-803">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5117a-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
