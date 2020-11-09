---
title: Host genérico .NET no ASP.NET Core
author: rick-anderson
description: Use o host genérico do .NET Core em aplicativos ASP.NET Core.  O host genérico é responsável pela inicialização do aplicativo e pelo gerenciamento de tempo de vida.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/host/generic-host
ms.openlocfilehash: 3e44932c302713132a37534b97fffdd91acce2c7
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234550"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="12ad2-104">Host genérico .NET no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12ad2-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12ad2-105">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="12ad2-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="12ad2-106">Este tópico fornece informações sobre como usar o host genérico .NET no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12ad2-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="12ad2-107">Para obter informações sobre como usar o host genérico .NET em aplicativos de console, consulte [host genérico .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="12ad2-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="12ad2-108">Definição de host</span><span class="sxs-lookup"><span data-stu-id="12ad2-108">Host definition</span></span>

<span data-ttu-id="12ad2-109">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="12ad2-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="12ad2-110">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="12ad2-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="12ad2-111">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="12ad2-111">Logging</span></span>
* <span data-ttu-id="12ad2-112">Configuração</span><span class="sxs-lookup"><span data-stu-id="12ad2-112">Configuration</span></span>
* <span data-ttu-id="12ad2-113">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="12ad2-113">`IHostedService` implementations</span></span>

<span data-ttu-id="12ad2-114">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12ad2-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="12ad2-115">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="12ad2-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="12ad2-116">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="12ad2-117">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="12ad2-117">Set up a host</span></span>

<span data-ttu-id="12ad2-118">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="12ad2-119">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-119">The `Main` method:</span></span>

* <span data-ttu-id="12ad2-120">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="12ad2-121">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="12ad2-122">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="12ad2-123">O código a seguir cria uma carga de trabalho não HTTP com uma `IHostedService` implementação adicionada ao contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="12ad2-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="12ad2-124">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="12ad2-125">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="12ad2-126">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="12ad2-127">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="12ad2-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="12ad2-128">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="12ad2-128">Default builder settings</span></span>

<span data-ttu-id="12ad2-129">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="12ad2-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="12ad2-130">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="12ad2-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="12ad2-131">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="12ad2-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="12ad2-132">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="12ad2-133">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="12ad2-133">Command-line arguments.</span></span>
* <span data-ttu-id="12ad2-134">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="12ad2-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="12ad2-135">*appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-135">*appsettings.json* .</span></span>
  * <span data-ttu-id="12ad2-136">*appsettings.{Environment}.json* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-136">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="12ad2-137">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="12ad2-138">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-138">Environment variables.</span></span>
  * <span data-ttu-id="12ad2-139">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="12ad2-139">Command-line arguments.</span></span>
* <span data-ttu-id="12ad2-140">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="12ad2-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="12ad2-141">Console</span><span class="sxs-lookup"><span data-stu-id="12ad2-141">Console</span></span>
  * <span data-ttu-id="12ad2-142">Depurar</span><span class="sxs-lookup"><span data-stu-id="12ad2-142">Debug</span></span>
  * <span data-ttu-id="12ad2-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="12ad2-143">EventSource</span></span>
  * <span data-ttu-id="12ad2-144">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="12ad2-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="12ad2-145">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="12ad2-146">O método <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>:</span><span class="sxs-lookup"><span data-stu-id="12ad2-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="12ad2-147">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="12ad2-148">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="12ad2-149">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="12ad2-150">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="12ad2-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="12ad2-151">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="12ad2-152">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="12ad2-152">Enables IIS integration.</span></span> <span data-ttu-id="12ad2-153">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="12ad2-154">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="12ad2-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="12ad2-155">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="12ad2-155">Framework-provided services</span></span>

<span data-ttu-id="12ad2-156">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="12ad2-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="12ad2-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="12ad2-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="12ad2-159">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12ad2-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="12ad2-160">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="12ad2-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="12ad2-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="12ad2-162">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="12ad2-163">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="12ad2-164">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="12ad2-165">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="12ad2-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="12ad2-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-166">IHostLifetime</span></span>

<span data-ttu-id="12ad2-167">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="12ad2-168">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-168">The last implementation registered is used.</span></span>

<span data-ttu-id="12ad2-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="12ad2-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="12ad2-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="12ad2-171">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="12ad2-172">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="12ad2-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="12ad2-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12ad2-173">IHostEnvironment</span></span>

<span data-ttu-id="12ad2-174">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="12ad2-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="12ad2-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12ad2-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="12ad2-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12ad2-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="12ad2-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="12ad2-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="12ad2-178">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="12ad2-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="12ad2-179">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="12ad2-179">Host configuration</span></span>

<span data-ttu-id="12ad2-180">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="12ad2-181">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="12ad2-182">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="12ad2-183">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="12ad2-184">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12ad2-185">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="12ad2-186">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="12ad2-187">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="12ad2-188">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="12ad2-189">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="12ad2-190">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="12ad2-191">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="12ad2-191">App configuration</span></span>

<span data-ttu-id="12ad2-192">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="12ad2-193">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12ad2-194">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="12ad2-195">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="12ad2-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="12ad2-196">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="12ad2-197">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="12ad2-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="12ad2-198">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="12ad2-198">Settings for all app types</span></span>

<span data-ttu-id="12ad2-199">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="12ad2-200">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="12ad2-201">Para obter mais informações, consulte a seção [configurações padrão do Construtor](#default-builder-settings) .</span><span class="sxs-lookup"><span data-stu-id="12ad2-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="12ad2-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12ad2-202">ApplicationName</span></span>

<span data-ttu-id="12ad2-203">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="12ad2-204">**Chave** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="12ad2-204">**Key** : `applicationName`</span></span>  
<span data-ttu-id="12ad2-205">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-205">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-206">**Padrão** : o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-206">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="12ad2-207">**Variável de ambiente** : `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="12ad2-207">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="12ad2-208">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="12ad2-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-209">ContentRoot</span></span>

<span data-ttu-id="12ad2-210">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="12ad2-211">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="12ad2-212">**Chave** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="12ad2-212">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="12ad2-213">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-213">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-214">**Padrão** : a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="12ad2-214">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="12ad2-215">**Variável de ambiente** : `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-215">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="12ad2-216">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="12ad2-217">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="12ad2-217">For more information, see:</span></span>

* [<span data-ttu-id="12ad2-218">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="12ad2-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="12ad2-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="12ad2-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12ad2-220">EnvironmentName</span></span>

<span data-ttu-id="12ad2-221">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="12ad2-222">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="12ad2-223">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="12ad2-224">**Chave** : `environment`</span><span class="sxs-lookup"><span data-stu-id="12ad2-224">**Key** : `environment`</span></span>  
<span data-ttu-id="12ad2-225">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-225">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-226">**Padrão** : `Production`</span><span class="sxs-lookup"><span data-stu-id="12ad2-226">**Default** : `Production`</span></span>  
<span data-ttu-id="12ad2-227">**Variável de ambiente** : `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-227">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="12ad2-228">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="12ad2-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="12ad2-229">ShutdownTimeout</span></span>

<span data-ttu-id="12ad2-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="12ad2-231">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-231">The default value is five seconds.</span></span>  <span data-ttu-id="12ad2-232">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="12ad2-233">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="12ad2-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="12ad2-234">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="12ad2-235">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="12ad2-236">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="12ad2-237">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="12ad2-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="12ad2-238">**Chave** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="12ad2-238">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="12ad2-239">**Tipo** : `int`</span><span class="sxs-lookup"><span data-stu-id="12ad2-239">**Type** : `int`</span></span>  
<span data-ttu-id="12ad2-240">**Padrão** : 5 segundos</span><span class="sxs-lookup"><span data-stu-id="12ad2-240">**Default** : 5 seconds</span></span>  
<span data-ttu-id="12ad2-241">**Variável de ambiente** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-241">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="12ad2-242">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="12ad2-243">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="12ad2-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="12ad2-244">Desabilitar recarga de configuração de aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="12ad2-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="12ad2-245">Por [padrão](xref:fundamentals/configuration/index#default), *appsettings.json* e *appSettings. { Ambiente}. JSON* são recarregados quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="12ad2-246">Para desabilitar esse comportamento de recarregamento no ASP.NET Core 5,0 ou posterior, defina a `hostBuilder:reloadConfigOnChange` chave como `false` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="12ad2-247">**Chave** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="12ad2-247">**Key** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="12ad2-248">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-248">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-249">**Padrão** : `true`</span><span class="sxs-lookup"><span data-stu-id="12ad2-249">**Default** : `true`</span></span>  
<span data-ttu-id="12ad2-250">**Argumento de linha de comando** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="12ad2-250">**Command-line argument** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="12ad2-251">**Variável de ambiente** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="12ad2-251">**Environment variable** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="12ad2-252">O separador de dois-pontos ( `:` ) não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="12ad2-253">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="12ad2-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="12ad2-254">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="12ad2-254">Settings for web apps</span></span>

<span data-ttu-id="12ad2-255">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="12ad2-256">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="12ad2-257">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="12ad2-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="12ad2-258">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="12ad2-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="12ad2-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="12ad2-259">CaptureStartupErrors</span></span>

<span data-ttu-id="12ad2-260">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="12ad2-261">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="12ad2-262">**Chave** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="12ad2-262">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="12ad2-263">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-263">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-264">**Padrão** : o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-264">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="12ad2-265">**Variável de ambiente** : `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-265">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="12ad2-266">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="12ad2-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="12ad2-267">DetailedErrors</span></span>

<span data-ttu-id="12ad2-268">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="12ad2-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="12ad2-269">**Chave** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="12ad2-269">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="12ad2-270">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-270">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-271">**Padrão** : `false`</span><span class="sxs-lookup"><span data-stu-id="12ad2-271">**Default** : `false`</span></span>  
<span data-ttu-id="12ad2-272">**Variável de ambiente** : `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-272">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="12ad2-273">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="12ad2-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="12ad2-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="12ad2-275">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="12ad2-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="12ad2-276">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="12ad2-277">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="12ad2-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="12ad2-278">**Chave** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12ad2-278">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="12ad2-279">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-279">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-280">**Padrão** : cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="12ad2-280">**Default** : Empty string</span></span>  
<span data-ttu-id="12ad2-281">**Variável de ambiente** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12ad2-281">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="12ad2-282">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="12ad2-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="12ad2-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="12ad2-284">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="12ad2-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="12ad2-285">**Chave** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12ad2-285">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="12ad2-286">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-286">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-287">**Padrão** : cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="12ad2-287">**Default** : Empty string</span></span>  
<span data-ttu-id="12ad2-288">**Variável de ambiente** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12ad2-288">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="12ad2-289">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="12ad2-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="12ad2-290">HTTPS_Port</span></span>

<span data-ttu-id="12ad2-291">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="12ad2-291">The HTTPS redirect port.</span></span> <span data-ttu-id="12ad2-292">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="12ad2-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="12ad2-293">**Chave** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="12ad2-293">**Key** : `https_port`</span></span>  
<span data-ttu-id="12ad2-294">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-294">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-295">**Padrão** : um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-295">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="12ad2-296">**Variável de ambiente** : `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-296">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="12ad2-297">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="12ad2-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="12ad2-298">PreferHostingUrls</span></span>

<span data-ttu-id="12ad2-299">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="12ad2-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="12ad2-300">**Chave** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="12ad2-300">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="12ad2-301">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-301">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-302">**Padrão** : `true`</span><span class="sxs-lookup"><span data-stu-id="12ad2-302">**Default** : `true`</span></span>  
<span data-ttu-id="12ad2-303">**Variável de ambiente** : `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-303">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="12ad2-304">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="12ad2-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="12ad2-305">PreventHostingStartup</span></span>

<span data-ttu-id="12ad2-306">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="12ad2-307">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="12ad2-308">**Chave** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="12ad2-308">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="12ad2-309">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-309">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-310">**Padrão** : `false`</span><span class="sxs-lookup"><span data-stu-id="12ad2-310">**Default** : `false`</span></span>  
<span data-ttu-id="12ad2-311">**Variável de ambiente** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="12ad2-311">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="12ad2-312">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="12ad2-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="12ad2-313">StartupAssembly</span></span>

<span data-ttu-id="12ad2-314">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="12ad2-315">**Chave** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="12ad2-315">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="12ad2-316">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-316">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-317">**Padrão** : o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="12ad2-317">**Default** : The app's assembly</span></span>  
<span data-ttu-id="12ad2-318">**Variável de ambiente** : `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="12ad2-318">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="12ad2-319">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="12ad2-320">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="12ad2-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="12ad2-321">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="12ad2-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="12ad2-322">URLs</span><span class="sxs-lookup"><span data-stu-id="12ad2-322">URLs</span></span>

<span data-ttu-id="12ad2-323">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="12ad2-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="12ad2-324">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="12ad2-325">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="12ad2-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="12ad2-326">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="12ad2-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="12ad2-327">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="12ad2-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="12ad2-328">**Chave** : `urls`</span><span class="sxs-lookup"><span data-stu-id="12ad2-328">**Key** : `urls`</span></span>  
<span data-ttu-id="12ad2-329">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-329">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-330">**Padrão** : `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="12ad2-330">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="12ad2-331">**Variável de ambiente** : `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-331">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="12ad2-332">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="12ad2-333">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="12ad2-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="12ad2-334">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="12ad2-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-335">WebRoot</span></span>

<span data-ttu-id="12ad2-336">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="12ad2-337">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="12ad2-338">**Chave** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="12ad2-338">**Key** : `webroot`</span></span>  
<span data-ttu-id="12ad2-339">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-339">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-340">**Padrão** : o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-340">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="12ad2-341">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="12ad2-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="12ad2-342">**Variável de ambiente** : `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-342">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="12ad2-343">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="12ad2-344">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="12ad2-344">For more information, see:</span></span>

* [<span data-ttu-id="12ad2-345">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="12ad2-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="12ad2-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="12ad2-347">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="12ad2-347">Manage the host lifetime</span></span>

<span data-ttu-id="12ad2-348">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="12ad2-349">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12ad2-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="12ad2-350">Executar</span><span class="sxs-lookup"><span data-stu-id="12ad2-350">Run</span></span>

<span data-ttu-id="12ad2-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="12ad2-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-352">RunAsync</span></span>

<span data-ttu-id="12ad2-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="12ad2-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-354">RunConsoleAsync</span></span>

<span data-ttu-id="12ad2-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="12ad2-356">Iniciar</span><span class="sxs-lookup"><span data-stu-id="12ad2-356">Start</span></span>

<span data-ttu-id="12ad2-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="12ad2-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="12ad2-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-358">StartAsync</span></span>

<span data-ttu-id="12ad2-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="12ad2-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="12ad2-361">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="12ad2-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-362">StopAsync</span></span>

<span data-ttu-id="12ad2-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="12ad2-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="12ad2-364">WaitForShutdown</span></span>

<span data-ttu-id="12ad2-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12ad2-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="12ad2-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="12ad2-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="12ad2-368">Controle externo</span><span class="sxs-lookup"><span data-stu-id="12ad2-368">External control</span></span>

<span data-ttu-id="12ad2-369">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="12ad2-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="12ad2-370">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="12ad2-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="12ad2-371">Este tópico fornece informações sobre como usar o host genérico .NET no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12ad2-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="12ad2-372">Para obter informações sobre como usar o host genérico .NET em aplicativos de console, consulte [host genérico .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="12ad2-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="12ad2-373">Definição de host</span><span class="sxs-lookup"><span data-stu-id="12ad2-373">Host definition</span></span>

<span data-ttu-id="12ad2-374">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="12ad2-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="12ad2-375">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="12ad2-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="12ad2-376">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="12ad2-376">Logging</span></span>
* <span data-ttu-id="12ad2-377">Configuração</span><span class="sxs-lookup"><span data-stu-id="12ad2-377">Configuration</span></span>
* <span data-ttu-id="12ad2-378">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="12ad2-378">`IHostedService` implementations</span></span>

<span data-ttu-id="12ad2-379">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12ad2-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="12ad2-380">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="12ad2-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="12ad2-381">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="12ad2-382">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="12ad2-382">Set up a host</span></span>

<span data-ttu-id="12ad2-383">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="12ad2-384">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-384">The `Main` method:</span></span>

* <span data-ttu-id="12ad2-385">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="12ad2-386">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="12ad2-387">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host genérico:</span><span class="sxs-lookup"><span data-stu-id="12ad2-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="12ad2-388">O código a seguir cria um host genérico usando carga de trabalho não HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="12ad2-389">A `IHostedService` implementação é adicionada ao contêiner di:</span><span class="sxs-lookup"><span data-stu-id="12ad2-389">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="12ad2-390">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="12ad2-391">O código anterior é gerado pelos modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12ad2-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="12ad2-392">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="12ad2-393">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="12ad2-394">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="12ad2-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="12ad2-395">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="12ad2-395">Default builder settings</span></span>

<span data-ttu-id="12ad2-396">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="12ad2-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="12ad2-397">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="12ad2-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="12ad2-398">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="12ad2-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="12ad2-399">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="12ad2-400">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="12ad2-400">Command-line arguments.</span></span>
* <span data-ttu-id="12ad2-401">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="12ad2-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="12ad2-402">*appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-402">*appsettings.json* .</span></span>
  * <span data-ttu-id="12ad2-403">*appsettings.{Environment}.json* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-403">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="12ad2-404">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-404">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="12ad2-405">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-405">Environment variables.</span></span>
  * <span data-ttu-id="12ad2-406">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="12ad2-406">Command-line arguments.</span></span>
* <span data-ttu-id="12ad2-407">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="12ad2-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="12ad2-408">Console</span><span class="sxs-lookup"><span data-stu-id="12ad2-408">Console</span></span>
  * <span data-ttu-id="12ad2-409">Depurar</span><span class="sxs-lookup"><span data-stu-id="12ad2-409">Debug</span></span>
  * <span data-ttu-id="12ad2-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="12ad2-410">EventSource</span></span>
  * <span data-ttu-id="12ad2-411">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="12ad2-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="12ad2-412">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="12ad2-413">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="12ad2-414">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="12ad2-415">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="12ad2-416">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="12ad2-417">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="12ad2-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="12ad2-418">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="12ad2-419">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="12ad2-419">Enables IIS integration.</span></span> <span data-ttu-id="12ad2-420">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="12ad2-421">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="12ad2-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="12ad2-422">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="12ad2-422">Framework-provided services</span></span>

<span data-ttu-id="12ad2-423">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="12ad2-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="12ad2-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="12ad2-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="12ad2-426">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12ad2-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="12ad2-427">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="12ad2-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="12ad2-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="12ad2-429">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="12ad2-430">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="12ad2-431">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="12ad2-432">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="12ad2-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="12ad2-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-433">IHostLifetime</span></span>

<span data-ttu-id="12ad2-434">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="12ad2-435">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-435">The last implementation registered is used.</span></span>

<span data-ttu-id="12ad2-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="12ad2-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="12ad2-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="12ad2-438">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="12ad2-439">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="12ad2-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="12ad2-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12ad2-440">IHostEnvironment</span></span>

<span data-ttu-id="12ad2-441">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="12ad2-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="12ad2-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12ad2-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="12ad2-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12ad2-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="12ad2-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="12ad2-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="12ad2-445">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="12ad2-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="12ad2-446">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="12ad2-446">Host configuration</span></span>

<span data-ttu-id="12ad2-447">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="12ad2-448">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="12ad2-449">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="12ad2-450">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="12ad2-451">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12ad2-452">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="12ad2-453">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="12ad2-454">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="12ad2-455">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="12ad2-456">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="12ad2-457">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="12ad2-458">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="12ad2-458">App configuration</span></span>

<span data-ttu-id="12ad2-459">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="12ad2-460">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12ad2-461">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="12ad2-462">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="12ad2-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="12ad2-463">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="12ad2-464">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="12ad2-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="12ad2-465">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="12ad2-465">Settings for all app types</span></span>

<span data-ttu-id="12ad2-466">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="12ad2-467">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="12ad2-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12ad2-468">ApplicationName</span></span>

<span data-ttu-id="12ad2-469">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="12ad2-470">**Chave** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="12ad2-470">**Key** : `applicationName`</span></span>  
<span data-ttu-id="12ad2-471">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-471">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-472">**Padrão** : o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-472">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="12ad2-473">**Variável de ambiente** : `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="12ad2-473">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="12ad2-474">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="12ad2-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-475">ContentRoot</span></span>

<span data-ttu-id="12ad2-476">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="12ad2-477">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="12ad2-478">**Chave** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="12ad2-478">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="12ad2-479">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-479">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-480">**Padrão** : a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="12ad2-480">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="12ad2-481">**Variável de ambiente** : `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-481">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="12ad2-482">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="12ad2-483">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="12ad2-483">For more information, see:</span></span>

* [<span data-ttu-id="12ad2-484">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="12ad2-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="12ad2-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="12ad2-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12ad2-486">EnvironmentName</span></span>

<span data-ttu-id="12ad2-487">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="12ad2-488">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="12ad2-489">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="12ad2-490">**Chave** : `environment`</span><span class="sxs-lookup"><span data-stu-id="12ad2-490">**Key** : `environment`</span></span>  
<span data-ttu-id="12ad2-491">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-491">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-492">**Padrão** : `Production`</span><span class="sxs-lookup"><span data-stu-id="12ad2-492">**Default** : `Production`</span></span>  
<span data-ttu-id="12ad2-493">**Variável de ambiente** : `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-493">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="12ad2-494">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="12ad2-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="12ad2-495">ShutdownTimeout</span></span>

<span data-ttu-id="12ad2-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="12ad2-497">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-497">The default value is five seconds.</span></span>  <span data-ttu-id="12ad2-498">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="12ad2-499">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="12ad2-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="12ad2-500">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="12ad2-501">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="12ad2-502">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="12ad2-503">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="12ad2-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="12ad2-504">**Chave** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="12ad2-504">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="12ad2-505">**Tipo** : `int`</span><span class="sxs-lookup"><span data-stu-id="12ad2-505">**Type** : `int`</span></span>  
<span data-ttu-id="12ad2-506">**Padrão** : 5 segundos</span><span class="sxs-lookup"><span data-stu-id="12ad2-506">**Default** : 5 seconds</span></span>  
<span data-ttu-id="12ad2-507">**Variável de ambiente** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-507">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="12ad2-508">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="12ad2-509">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="12ad2-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="12ad2-510">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="12ad2-510">Settings for web apps</span></span>

<span data-ttu-id="12ad2-511">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="12ad2-512">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="12ad2-513">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="12ad2-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="12ad2-514">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="12ad2-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="12ad2-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="12ad2-515">CaptureStartupErrors</span></span>

<span data-ttu-id="12ad2-516">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="12ad2-517">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="12ad2-518">**Chave** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="12ad2-518">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="12ad2-519">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-519">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-520">**Padrão** : o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-520">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="12ad2-521">**Variável de ambiente** : `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-521">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="12ad2-522">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="12ad2-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="12ad2-523">DetailedErrors</span></span>

<span data-ttu-id="12ad2-524">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="12ad2-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="12ad2-525">**Chave** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="12ad2-525">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="12ad2-526">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-526">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-527">**Padrão** : `false`</span><span class="sxs-lookup"><span data-stu-id="12ad2-527">**Default** : `false`</span></span>  
<span data-ttu-id="12ad2-528">**Variável de ambiente** : `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-528">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="12ad2-529">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="12ad2-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="12ad2-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="12ad2-531">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="12ad2-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="12ad2-532">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="12ad2-533">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="12ad2-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="12ad2-534">**Chave** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12ad2-534">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="12ad2-535">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-535">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-536">**Padrão** : cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="12ad2-536">**Default** : Empty string</span></span>  
<span data-ttu-id="12ad2-537">**Variável de ambiente** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12ad2-537">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="12ad2-538">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="12ad2-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="12ad2-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="12ad2-540">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="12ad2-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="12ad2-541">**Chave** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12ad2-541">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="12ad2-542">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-542">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-543">**Padrão** : cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="12ad2-543">**Default** : Empty string</span></span>  
<span data-ttu-id="12ad2-544">**Variável de ambiente** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12ad2-544">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="12ad2-545">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="12ad2-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="12ad2-546">HTTPS_Port</span></span>

<span data-ttu-id="12ad2-547">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="12ad2-547">The HTTPS redirect port.</span></span> <span data-ttu-id="12ad2-548">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="12ad2-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="12ad2-549">**Chave** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="12ad2-549">**Key** : `https_port`</span></span>  
<span data-ttu-id="12ad2-550">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-550">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-551">**Padrão** : um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-551">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="12ad2-552">**Variável de ambiente** : `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-552">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="12ad2-553">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="12ad2-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="12ad2-554">PreferHostingUrls</span></span>

<span data-ttu-id="12ad2-555">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="12ad2-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="12ad2-556">**Chave** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="12ad2-556">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="12ad2-557">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-557">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-558">**Padrão** : `true`</span><span class="sxs-lookup"><span data-stu-id="12ad2-558">**Default** : `true`</span></span>  
<span data-ttu-id="12ad2-559">**Variável de ambiente** : `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-559">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="12ad2-560">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="12ad2-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="12ad2-561">PreventHostingStartup</span></span>

<span data-ttu-id="12ad2-562">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="12ad2-563">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="12ad2-564">**Chave** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="12ad2-564">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="12ad2-565">**Tipo** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="12ad2-565">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12ad2-566">**Padrão** : `false`</span><span class="sxs-lookup"><span data-stu-id="12ad2-566">**Default** : `false`</span></span>  
<span data-ttu-id="12ad2-567">**Variável de ambiente** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="12ad2-567">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="12ad2-568">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="12ad2-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="12ad2-569">StartupAssembly</span></span>

<span data-ttu-id="12ad2-570">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="12ad2-571">**Chave** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="12ad2-571">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="12ad2-572">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-572">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-573">**Padrão** : o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="12ad2-573">**Default** : The app's assembly</span></span>  
<span data-ttu-id="12ad2-574">**Variável de ambiente** : `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="12ad2-574">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="12ad2-575">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="12ad2-576">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="12ad2-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="12ad2-577">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="12ad2-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="12ad2-578">URLs</span><span class="sxs-lookup"><span data-stu-id="12ad2-578">URLs</span></span>

<span data-ttu-id="12ad2-579">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="12ad2-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="12ad2-580">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="12ad2-581">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="12ad2-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="12ad2-582">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="12ad2-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="12ad2-583">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="12ad2-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="12ad2-584">**Chave** : `urls`</span><span class="sxs-lookup"><span data-stu-id="12ad2-584">**Key** : `urls`</span></span>  
<span data-ttu-id="12ad2-585">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-585">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-586">**Padrão** : `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="12ad2-586">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="12ad2-587">**Variável de ambiente** : `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="12ad2-587">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="12ad2-588">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="12ad2-589">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="12ad2-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="12ad2-590">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="12ad2-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-591">WebRoot</span></span>

<span data-ttu-id="12ad2-592">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="12ad2-593">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="12ad2-594">**Chave** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="12ad2-594">**Key** : `webroot`</span></span>  
<span data-ttu-id="12ad2-595">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-595">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-596">**Padrão** : o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="12ad2-596">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="12ad2-597">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="12ad2-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="12ad2-598">**Variável de ambiente** : `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="12ad2-598">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="12ad2-599">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="12ad2-600">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="12ad2-600">For more information, see:</span></span>

* [<span data-ttu-id="12ad2-601">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="12ad2-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="12ad2-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12ad2-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="12ad2-603">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="12ad2-603">Manage the host lifetime</span></span>

<span data-ttu-id="12ad2-604">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="12ad2-605">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12ad2-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="12ad2-606">Executar</span><span class="sxs-lookup"><span data-stu-id="12ad2-606">Run</span></span>

<span data-ttu-id="12ad2-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="12ad2-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-608">RunAsync</span></span>

<span data-ttu-id="12ad2-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="12ad2-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-610">RunConsoleAsync</span></span>

<span data-ttu-id="12ad2-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="12ad2-612">Iniciar</span><span class="sxs-lookup"><span data-stu-id="12ad2-612">Start</span></span>

<span data-ttu-id="12ad2-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="12ad2-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="12ad2-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-614">StartAsync</span></span>

<span data-ttu-id="12ad2-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="12ad2-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="12ad2-617">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="12ad2-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-618">StopAsync</span></span>

<span data-ttu-id="12ad2-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="12ad2-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="12ad2-620">WaitForShutdown</span></span>

<span data-ttu-id="12ad2-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12ad2-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="12ad2-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="12ad2-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="12ad2-624">Controle externo</span><span class="sxs-lookup"><span data-stu-id="12ad2-624">External control</span></span>

<span data-ttu-id="12ad2-625">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="12ad2-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="12ad2-626">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="12ad2-627">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="12ad2-628">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="12ad2-629">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="12ad2-630">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="12ad2-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="12ad2-631">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="12ad2-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="12ad2-632">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="12ad2-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="12ad2-633">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="12ad2-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="12ad2-634">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12ad2-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="12ad2-635">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal* .</span></span> <span data-ttu-id="12ad2-636">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="12ad2-637">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="12ad2-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="12ad2-638">Abra o arquivo *.vscode/launch.json* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="12ad2-639">Na configuração **Inicialização do .NET Core (console)** , localize a entrada **console** .</span><span class="sxs-lookup"><span data-stu-id="12ad2-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="12ad2-640">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="12ad2-641">Introdução</span><span class="sxs-lookup"><span data-stu-id="12ad2-641">Introduction</span></span>

<span data-ttu-id="12ad2-642">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="12ad2-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="12ad2-643">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="12ad2-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="12ad2-644"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="12ad2-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="12ad2-645">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="12ad2-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="12ad2-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="12ad2-647">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="12ad2-647">Set up a host</span></span>

<span data-ttu-id="12ad2-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="12ad2-649">Opções</span><span class="sxs-lookup"><span data-stu-id="12ad2-649">Options</span></span>

<span data-ttu-id="12ad2-650">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="12ad2-651">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="12ad2-651">Shutdown timeout</span></span>

<span data-ttu-id="12ad2-652">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="12ad2-653">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-653">The default value is five seconds.</span></span>

<span data-ttu-id="12ad2-654">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundo:</span><span class="sxs-lookup"><span data-stu-id="12ad2-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="12ad2-655">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="12ad2-655">Default services</span></span>

<span data-ttu-id="12ad2-656">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="12ad2-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="12ad2-657">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="12ad2-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="12ad2-658">[Configuração](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="12ad2-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="12ad2-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="12ad2-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="12ad2-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="12ad2-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="12ad2-661">[Opções](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="12ad2-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="12ad2-662">[Registro em log](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="12ad2-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="12ad2-663">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="12ad2-663">Host configuration</span></span>

<span data-ttu-id="12ad2-664">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="12ad2-664">Host configuration is created by:</span></span>

* <span data-ttu-id="12ad2-665">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="12ad2-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="12ad2-666">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="12ad2-667">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="12ad2-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="12ad2-668">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="12ad2-668">Application key (name)</span></span>

<span data-ttu-id="12ad2-669">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="12ad2-670">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="12ad2-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="12ad2-671">**Chave** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="12ad2-671">**Key** : `applicationName`</span></span>  
<span data-ttu-id="12ad2-672">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-672">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-673">**Padrão** : o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-673">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="12ad2-674">**Definir usando** : `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="12ad2-674">**Set using** : `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="12ad2-675">**Variável de ambiente** : `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="12ad2-675">**Environment variable** : `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="12ad2-676">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="12ad2-676">Content root</span></span>

<span data-ttu-id="12ad2-677">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="12ad2-678">**Chave** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="12ad2-678">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="12ad2-679">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-679">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-680">**Padrão** : o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="12ad2-680">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="12ad2-681">**Definir usando** : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="12ad2-681">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="12ad2-682">**Variável de ambiente** : `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="12ad2-682">**Environment variable** : `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="12ad2-683">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="12ad2-684">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="12ad2-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="12ad2-685">Ambiente</span><span class="sxs-lookup"><span data-stu-id="12ad2-685">Environment</span></span>

<span data-ttu-id="12ad2-686">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="12ad2-687">**Chave** : `environment`</span><span class="sxs-lookup"><span data-stu-id="12ad2-687">**Key** : `environment`</span></span>  
<span data-ttu-id="12ad2-688">**Tipo** : `string`</span><span class="sxs-lookup"><span data-stu-id="12ad2-688">**Type** : `string`</span></span>  
<span data-ttu-id="12ad2-689">**Padrão** : `Production`</span><span class="sxs-lookup"><span data-stu-id="12ad2-689">**Default** : `Production`</span></span>  
<span data-ttu-id="12ad2-690">**Definir usando** : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="12ad2-690">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="12ad2-691">**Variável de ambiente** : `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="12ad2-691">**Environment variable** : `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="12ad2-692">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="12ad2-692">The environment can be set to any value.</span></span> <span data-ttu-id="12ad2-693">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="12ad2-694">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="12ad2-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="12ad2-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="12ad2-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="12ad2-697">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="12ad2-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="12ad2-699">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="12ad2-700">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="12ad2-700">No providers are included by default.</span></span> <span data-ttu-id="12ad2-701">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="12ad2-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="12ad2-702">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json* ).</span><span class="sxs-lookup"><span data-stu-id="12ad2-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="12ad2-703">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-703">Environment variable configuration.</span></span>
* <span data-ttu-id="12ad2-704">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="12ad2-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="12ad2-705">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="12ad2-705">Any other required configuration providers.</span></span>

<span data-ttu-id="12ad2-706">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="12ad2-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="12ad2-707">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json* , e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-707">The sample app uses a JSON file, *hostsettings.json* , and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="12ad2-708">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="12ad2-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="12ad2-709">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="12ad2-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="12ad2-710">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="12ad2-711">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="12ad2-712">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="12ad2-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="12ad2-713">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="12ad2-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="12ad2-714">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="12ad2-715">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="12ad2-716">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="12ad2-717">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="12ad2-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="12ad2-718">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="12ad2-718">*hostsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="12ad2-719">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="12ad2-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="12ad2-720">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="12ad2-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="12ad2-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="12ad2-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="12ad2-722">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="12ad2-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="12ad2-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="12ad2-725">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="12ad2-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="12ad2-726">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="12ad2-727">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="12ad2-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="12ad2-728">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="12ad2-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="12ad2-729">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="12ad2-729">*appsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="12ad2-730">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="12ad2-730">*appsettings.Development.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="12ad2-731">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="12ad2-731">*appsettings.Production.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="12ad2-732">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="12ad2-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="12ad2-733">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="12ad2-734">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="12ad2-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="12ad2-735">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="12ad2-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="12ad2-736">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="12ad2-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="12ad2-737">ConfigureServices</span></span>

<span data-ttu-id="12ad2-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="12ad2-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="12ad2-740">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="12ad2-741">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="12ad2-742">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="12ad2-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="12ad2-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="12ad2-743">ConfigureLogging</span></span>

<span data-ttu-id="12ad2-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="12ad2-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="12ad2-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-746">UseConsoleLifetime</span></span>

<span data-ttu-id="12ad2-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="12ad2-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="12ad2-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="12ad2-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="12ad2-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="12ad2-750">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="12ad2-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="12ad2-751">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="12ad2-751">Container configuration</span></span>

<span data-ttu-id="12ad2-752">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="12ad2-753">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="12ad2-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="12ad2-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="12ad2-755">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="12ad2-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="12ad2-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="12ad2-758">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="12ad2-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="12ad2-759">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="12ad2-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="12ad2-760">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="12ad2-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="12ad2-761">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="12ad2-761">Extensibility</span></span>

<span data-ttu-id="12ad2-762">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="12ad2-763">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="12ad2-764">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="12ad2-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="12ad2-765">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="12ad2-765">Manage the host</span></span>

<span data-ttu-id="12ad2-766">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12ad2-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="12ad2-767">Executar</span><span class="sxs-lookup"><span data-stu-id="12ad2-767">Run</span></span>

<span data-ttu-id="12ad2-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="12ad2-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="12ad2-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-769">RunAsync</span></span>

<span data-ttu-id="12ad2-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="12ad2-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="12ad2-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-771">RunConsoleAsync</span></span>

<span data-ttu-id="12ad2-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="12ad2-773">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-773">Start and StopAsync</span></span>

<span data-ttu-id="12ad2-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="12ad2-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="12ad2-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="12ad2-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="12ad2-776">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="12ad2-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="12ad2-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="12ad2-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="12ad2-779">WaitForShutdown</span></span>

<span data-ttu-id="12ad2-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é disparado por meio do <xref:Microsoft.Extensions.Hosting.IHostLifetime> , como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta para <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="12ad2-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="12ad2-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="12ad2-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="12ad2-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="12ad2-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="12ad2-784">Controle externo</span><span class="sxs-lookup"><span data-stu-id="12ad2-784">External control</span></span>

<span data-ttu-id="12ad2-785">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="12ad2-785">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="12ad2-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="12ad2-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="12ad2-787">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="12ad2-788">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="12ad2-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="12ad2-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="12ad2-790">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="12ad2-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="12ad2-791">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="12ad2-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="12ad2-792">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12ad2-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="12ad2-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="12ad2-794">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="12ad2-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="12ad2-795">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="12ad2-795">Cancellation Token</span></span> | <span data-ttu-id="12ad2-796">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="12ad2-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="12ad2-797">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="12ad2-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="12ad2-798">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="12ad2-799">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-799">All requests should be processed.</span></span> <span data-ttu-id="12ad2-800">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="12ad2-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="12ad2-801">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="12ad2-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="12ad2-802">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="12ad2-802">Requests may still be processing.</span></span> <span data-ttu-id="12ad2-803">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="12ad2-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="12ad2-804">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="12ad2-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="12ad2-805">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="12ad2-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="12ad2-806">*LifetimeEventsHostedService.cs* :</span><span class="sxs-lookup"><span data-stu-id="12ad2-806">*LifetimeEventsHostedService.cs* :</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="12ad2-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12ad2-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="12ad2-808">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="12ad2-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="12ad2-809">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="12ad2-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
