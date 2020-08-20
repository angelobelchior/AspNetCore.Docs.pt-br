---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 8fa44f567906ecf36a9bbaa5076cd36c75c10781
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634872"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="1e324-103">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e324-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e324-104">Este artigo fornece uma visão geral dos principais tópicos para entender como desenvolver ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="1e324-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="1e324-105">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="1e324-105">The Startup class</span></span>

<span data-ttu-id="1e324-106">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="1e324-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="1e324-107">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="1e324-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="1e324-108">O pipeline de tratamento de solicitação do aplicativo é definido como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="1e324-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="1e324-109">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="1e324-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="1e324-110">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1e324-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="1e324-111">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="1e324-111">Dependency injection (services)</span></span>

<span data-ttu-id="1e324-112">ASP.NET Core inclui uma estrutura de injeção de dependência interna (DI) que torna os serviços configurados disponíveis em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1e324-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="1e324-113">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="1e324-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="1e324-114">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1e324-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1e324-115">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1e324-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="1e324-116">Normalmente, os serviços são resolvidos de DI usando injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="1e324-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="1e324-117">Com a injeção de construtor, uma classe declara um parâmetro de construtor do tipo necessário ou de uma interface.</span><span class="sxs-lookup"><span data-stu-id="1e324-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="1e324-118">A estrutura DI fornece uma instância desse serviço em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="1e324-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="1e324-119">O exemplo a seguir usa injeção de construtor para resolver um `RazorPagesMovieContext` de di:</span><span class="sxs-lookup"><span data-stu-id="1e324-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="1e324-120">Se o contêiner interno de inversão de controle (IoC) não atender a todas as necessidades de um aplicativo, um contêiner IoC de terceiros poderá ser usado em vez disso.</span><span class="sxs-lookup"><span data-stu-id="1e324-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="1e324-121">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1e324-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="1e324-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="1e324-122">Middleware</span></span>

<span data-ttu-id="1e324-123">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="1e324-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="1e324-124">Cada componente executa operações em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="1e324-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="1e324-125">Por convenção, um componente de middleware é adicionado ao pipeline invocando um `Use...` método de extensão no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="1e324-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="1e324-126">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="1e324-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="1e324-127">O exemplo a seguir configura um pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="1e324-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="1e324-128">O ASP.NET Core inclui um conjunto avançado de middleware interno.</span><span class="sxs-lookup"><span data-stu-id="1e324-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="1e324-129">Os componentes de middleware personalizados também podem ser escritos.</span><span class="sxs-lookup"><span data-stu-id="1e324-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="1e324-130">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="1e324-131">Host</span><span class="sxs-lookup"><span data-stu-id="1e324-131">Host</span></span>

<span data-ttu-id="1e324-132">Na inicialização, um aplicativo ASP.NET Core cria um *host*.</span><span class="sxs-lookup"><span data-stu-id="1e324-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="1e324-133">O host encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="1e324-134">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="1e324-134">An HTTP server implementation</span></span>
* <span data-ttu-id="1e324-135">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="1e324-135">Middleware components</span></span>
* <span data-ttu-id="1e324-136">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="1e324-136">Logging</span></span>
* <span data-ttu-id="1e324-137">Serviços de injeção de dependência (DI)</span><span class="sxs-lookup"><span data-stu-id="1e324-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="1e324-138">Configuração</span><span class="sxs-lookup"><span data-stu-id="1e324-138">Configuration</span></span>

<span data-ttu-id="1e324-139">Há dois hosts diferentes:</span><span class="sxs-lookup"><span data-stu-id="1e324-139">There are two different hosts:</span></span> 

* <span data-ttu-id="1e324-140">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="1e324-140">.NET Generic Host</span></span>
* <span data-ttu-id="1e324-141">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e324-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="1e324-142">O host genérico .NET é recomendado.</span><span class="sxs-lookup"><span data-stu-id="1e324-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="1e324-143">O host Web do ASP.NET Core está disponível apenas para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="1e324-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="1e324-144">O exemplo a seguir cria um host genérico .NET:</span><span class="sxs-lookup"><span data-stu-id="1e324-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="1e324-145">Os `CreateDefaultBuilder` `ConfigureWebHostDefaults` métodos e configuram um host com um conjunto de opções padrão, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="1e324-146">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="1e324-147">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="1e324-148">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="1e324-149">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1e324-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="1e324-150">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="1e324-150">Non-web scenarios</span></span>

<span data-ttu-id="1e324-151">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="1e324-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="1e324-152">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="1e324-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="1e324-153">Servidores</span><span class="sxs-lookup"><span data-stu-id="1e324-153">Servers</span></span>

<span data-ttu-id="1e324-154">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e324-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="1e324-155">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="1e324-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="1e324-156">Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="1e324-157">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="1e324-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="1e324-158">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="1e324-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="1e324-159">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="1e324-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="1e324-160">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="1e324-161">O *servidor http do IIS* é um servidor para Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="1e324-162">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="1e324-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="1e324-163">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="1e324-164">macOS</span><span class="sxs-lookup"><span data-stu-id="1e324-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="1e324-165">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="1e324-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1e324-166">No ASP.NET Core 2,0 ou posterior, o Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1e324-167">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1e324-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="1e324-168">Linux</span><span class="sxs-lookup"><span data-stu-id="1e324-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="1e324-169">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="1e324-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1e324-170">No ASP.NET Core 2,0 ou posterior, o Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1e324-171">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1e324-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="1e324-172">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="1e324-173">Configuração</span><span class="sxs-lookup"><span data-stu-id="1e324-173">Configuration</span></span>

<span data-ttu-id="1e324-174">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="1e324-175">Os provedores de configuração internos estão disponíveis para uma variedade de fontes, como arquivos *. JSON* , arquivos *. xml* , variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="1e324-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="1e324-176">Escreva provedores de configuração personalizados para dar suporte a outras fontes.</span><span class="sxs-lookup"><span data-stu-id="1e324-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="1e324-177">Por [padrão](xref:fundamentals/configuration/index#default), os aplicativos ASP.NET Core são configurados para ler de *appsettings.js*, variáveis de ambiente, linha de comando e muito mais.</span><span class="sxs-lookup"><span data-stu-id="1e324-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="1e324-178">Quando a configuração do aplicativo é carregada, os valores das variáveis de ambiente substituem valores de *appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="1e324-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="1e324-179">A maneira preferida de ler valores de configuração relacionados é usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="1e324-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="1e324-180">Para obter mais informações, consulte [associar dados de configuração hierárquica usando o padrão de opções](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="1e324-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="1e324-181">Para gerenciar dados de configuração confidenciais, como senhas, ASP.NET Core fornece o [Gerenciador de segredo](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="1e324-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="1e324-182">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e324-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="1e324-183">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="1e324-184">Ambientes</span><span class="sxs-lookup"><span data-stu-id="1e324-184">Environments</span></span>

<span data-ttu-id="1e324-185">Ambientes de execução, como `Development` , `Staging` e `Production` , são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e324-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="1e324-186">Especifique o ambiente em que um aplicativo está sendo executado definindo a `ASPNETCORE_ENVIRONMENT` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e324-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="1e324-187">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="1e324-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="1e324-188">Essa implementação está disponível em qualquer lugar em um aplicativo por meio de injeção de dependência (DI).</span><span class="sxs-lookup"><span data-stu-id="1e324-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="1e324-189">O exemplo a seguir configura o aplicativo para fornecer informações de erro detalhadas durante a execução no `Development` ambiente:</span><span class="sxs-lookup"><span data-stu-id="1e324-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="1e324-190">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1e324-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="1e324-191">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="1e324-191">Logging</span></span>

<span data-ttu-id="1e324-192">O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros.</span><span class="sxs-lookup"><span data-stu-id="1e324-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1e324-193">Os provedores disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="1e324-193">Available providers include:</span></span>

* <span data-ttu-id="1e324-194">Console</span><span class="sxs-lookup"><span data-stu-id="1e324-194">Console</span></span>
* <span data-ttu-id="1e324-195">Depurar</span><span class="sxs-lookup"><span data-stu-id="1e324-195">Debug</span></span>
* <span data-ttu-id="1e324-196">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="1e324-197">Log de eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-197">Windows Event Log</span></span>
* <span data-ttu-id="1e324-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1e324-198">TraceSource</span></span>
* <span data-ttu-id="1e324-199">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="1e324-199">Azure App Service</span></span>
* <span data-ttu-id="1e324-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1e324-200">Azure Application Insights</span></span>

<span data-ttu-id="1e324-201">Para criar logs, resolva um <xref:Microsoft.Extensions.Logging.ILogger%601> serviço de injeção de dependência (di) e métodos de registro em log de chamada, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> .</span><span class="sxs-lookup"><span data-stu-id="1e324-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="1e324-202">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1e324-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="1e324-203">Métodos de registro em log, como `LogInformation` suporte a qualquer número de campos.</span><span class="sxs-lookup"><span data-stu-id="1e324-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="1e324-204">Esses campos são comumente usados para construir uma mensagem `string` , mas alguns provedores de log os enviam para um armazenamento de dados como campos separados.</span><span class="sxs-lookup"><span data-stu-id="1e324-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="1e324-205">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1e324-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1e324-206">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="1e324-207">Roteamento</span><span class="sxs-lookup"><span data-stu-id="1e324-207">Routing</span></span>

<span data-ttu-id="1e324-208">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="1e324-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="1e324-209">O manipulador é normalmente uma Razor página, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="1e324-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="1e324-210">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1e324-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="1e324-211">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1e324-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="1e324-212">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="1e324-212">Error handling</span></span>

<span data-ttu-id="1e324-213">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="1e324-214">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="1e324-214">A developer exception page</span></span>
* <span data-ttu-id="1e324-215">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="1e324-215">Custom error pages</span></span>
* <span data-ttu-id="1e324-216">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="1e324-216">Static status code pages</span></span>
* <span data-ttu-id="1e324-217">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="1e324-217">Startup exception handling</span></span>

<span data-ttu-id="1e324-218">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="1e324-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="1e324-219">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="1e324-219">Make HTTP requests</span></span>

<span data-ttu-id="1e324-220">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="1e324-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="1e324-221">O alocador:</span><span class="sxs-lookup"><span data-stu-id="1e324-221">The factory:</span></span>

* <span data-ttu-id="1e324-222">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="1e324-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="1e324-223">Por exemplo, registre e configure um cliente *GitHub* para acessar o github.</span><span class="sxs-lookup"><span data-stu-id="1e324-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="1e324-224">Registre e configure um cliente padrão para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="1e324-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="1e324-225">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="1e324-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="1e324-226">Esse padrão é semelhante ao pipeline de middleware de entrada de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e324-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="1e324-227">O padrão fornece um mecanismo para gerenciar preocupações abrangentes para solicitações HTTP, incluindo cache, tratamento de erros, serialização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="1e324-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="1e324-228">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="1e324-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="1e324-229">Gerencia o pooling e o tempo de vida de instâncias subjacentes `HttpClientHandler` para evitar problemas comuns de DNS que ocorrem durante o gerenciamento `HttpClient` manual de tempos de vida.</span><span class="sxs-lookup"><span data-stu-id="1e324-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="1e324-230">Adiciona uma experiência de registro configurável por meio <xref:Microsoft.Extensions.Logging.ILogger> de todas as solicitações enviadas por clientes criados pela fábrica.</span><span class="sxs-lookup"><span data-stu-id="1e324-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="1e324-231">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="1e324-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="1e324-232">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="1e324-232">Content root</span></span>

<span data-ttu-id="1e324-233">A raiz do conteúdo é o caminho base para:</span><span class="sxs-lookup"><span data-stu-id="1e324-233">The content root is the base path for:</span></span>

* <span data-ttu-id="1e324-234">O executável que hospeda o aplicativo (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="1e324-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="1e324-235">Assemblies compilados que compõem o aplicativo (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="1e324-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="1e324-236">Arquivos de conteúdo usados pelo aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="1e324-237">Razor arquivos (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="1e324-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="1e324-238">Arquivos de configuração (*. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="1e324-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="1e324-239">Arquivos de dados (*. db*)</span><span class="sxs-lookup"><span data-stu-id="1e324-239">Data files (*.db*)</span></span>
* <span data-ttu-id="1e324-240">A [raiz da Web](#web-root), normalmente a pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1e324-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="1e324-241">Durante o desenvolvimento, a raiz do conteúdo assume como padrão o diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1e324-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="1e324-242">Esse diretório também é o caminho base para os arquivos de conteúdo do aplicativo e a [raiz da Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="1e324-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="1e324-243">Especifique uma raiz de conteúdo diferente definindo seu caminho ao [compilar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="1e324-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="1e324-244">Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="1e324-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="1e324-245">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="1e324-245">Web root</span></span>

<span data-ttu-id="1e324-246">A raiz da Web é o caminho base para arquivos de recursos públicos e estáticos, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="1e324-247">Folhas de estilo (*. css*)</span><span class="sxs-lookup"><span data-stu-id="1e324-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="1e324-248">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="1e324-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="1e324-249">Imagens (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="1e324-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="1e324-250">Por padrão, os arquivos estáticos são servidos somente do diretório raiz da Web e de seus subdiretórios.</span><span class="sxs-lookup"><span data-stu-id="1e324-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="1e324-251">O caminho raiz da Web tem como padrão *{Content root}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1e324-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="1e324-252">Especifique uma raiz da Web diferente definindo seu caminho ao [compilar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="1e324-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="1e324-253">Para obter mais informações, confira [Diretório base](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="1e324-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="1e324-254">Impedir a publicação de arquivos em *wwwroot* com o [ \<Content> item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="1e324-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="1e324-255">O exemplo a seguir impede a publicação de conteúdo em *wwwroot/local* e em seus subdiretórios:</span><span class="sxs-lookup"><span data-stu-id="1e324-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="1e324-256">Em Razor arquivos *. cshtml* , a barra til ( `~/` ) aponta para a raiz da Web.</span><span class="sxs-lookup"><span data-stu-id="1e324-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="1e324-257">Um caminho que começa com `~/` é conhecido como um *caminho virtual*.</span><span class="sxs-lookup"><span data-stu-id="1e324-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="1e324-258">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1e324-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e324-259">Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e324-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="1e324-260">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="1e324-260">The Startup class</span></span>

<span data-ttu-id="1e324-261">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="1e324-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="1e324-262">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="1e324-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="1e324-263">O pipeline de tratamento de solicitação é definido.</span><span class="sxs-lookup"><span data-stu-id="1e324-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="1e324-264">*Serviços* são componentes usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1e324-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="1e324-265">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="1e324-265">For example, a logging component is a service.</span></span> <span data-ttu-id="1e324-266">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1e324-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="1e324-267">O pipeline de tratamento de solicitação é composto como uma série de componentes de *middleware* .</span><span class="sxs-lookup"><span data-stu-id="1e324-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="1e324-268">Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1e324-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="1e324-269">Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="1e324-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="1e324-270">O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1e324-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="1e324-271">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="1e324-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="1e324-272">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1e324-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="1e324-273">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="1e324-273">Dependency injection (services)</span></span>

<span data-ttu-id="1e324-274">O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1e324-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="1e324-275">Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário.</span><span class="sxs-lookup"><span data-stu-id="1e324-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="1e324-276">O parâmetro pode ser o tipo de serviço ou uma interface.</span><span class="sxs-lookup"><span data-stu-id="1e324-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="1e324-277">O sistema de DI fornece o serviço em runtime.</span><span class="sxs-lookup"><span data-stu-id="1e324-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="1e324-278">Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1e324-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="1e324-279">A linha realçada é um exemplo de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="1e324-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="1e324-280">Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.</span><span class="sxs-lookup"><span data-stu-id="1e324-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="1e324-281">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1e324-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="1e324-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="1e324-282">Middleware</span></span>

<span data-ttu-id="1e324-283">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="1e324-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="1e324-284">Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="1e324-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="1e324-285">Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1e324-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="1e324-286">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="1e324-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="1e324-287">O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="1e324-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="1e324-288">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="1e324-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="1e324-289">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="1e324-290">Host</span><span class="sxs-lookup"><span data-stu-id="1e324-290">Host</span></span>

<span data-ttu-id="1e324-291">Um aplicativo ASP.NET Core cria um *host* na inicialização.</span><span class="sxs-lookup"><span data-stu-id="1e324-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="1e324-292">O host é um objeto que encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="1e324-293">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="1e324-293">An HTTP server implementation</span></span>
* <span data-ttu-id="1e324-294">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="1e324-294">Middleware components</span></span>
* <span data-ttu-id="1e324-295">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="1e324-295">Logging</span></span>
* <span data-ttu-id="1e324-296">DI</span><span class="sxs-lookup"><span data-stu-id="1e324-296">DI</span></span>
* <span data-ttu-id="1e324-297">Configuração</span><span class="sxs-lookup"><span data-stu-id="1e324-297">Configuration</span></span>

<span data-ttu-id="1e324-298">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="1e324-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="1e324-299">Dois hosts estão disponíveis: o Host Web e o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="1e324-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="1e324-300">No ASP.NET Core 2.x, o Host Genérico é somente para cenários não Web.</span><span class="sxs-lookup"><span data-stu-id="1e324-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="1e324-301">O código para criar um host está em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1e324-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="1e324-302">O método `CreateDefaultBuilder` configura um host com as opções usadas com frequência, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="1e324-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="1e324-303">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="1e324-304">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="1e324-305">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="1e324-306">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="1e324-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="1e324-307">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="1e324-307">Non-web scenarios</span></span>

<span data-ttu-id="1e324-308">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="1e324-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="1e324-309">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="1e324-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="1e324-310">Servidores</span><span class="sxs-lookup"><span data-stu-id="1e324-310">Servers</span></span>

<span data-ttu-id="1e324-311">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e324-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="1e324-312">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="1e324-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="1e324-313">Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="1e324-314">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="1e324-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="1e324-315">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="1e324-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="1e324-316">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="1e324-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="1e324-317">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="1e324-318">O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="1e324-319">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="1e324-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="1e324-320">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="1e324-321">macOS</span><span class="sxs-lookup"><span data-stu-id="1e324-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="1e324-322">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="1e324-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1e324-323">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1e324-324">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1e324-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="1e324-325">Linux</span><span class="sxs-lookup"><span data-stu-id="1e324-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="1e324-326">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="1e324-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1e324-327">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1e324-328">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1e324-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="1e324-329">Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="1e324-330">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="1e324-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="1e324-331">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="1e324-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="1e324-332">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="1e324-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="1e324-333">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="1e324-334">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="1e324-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="1e324-335">macOS</span><span class="sxs-lookup"><span data-stu-id="1e324-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="1e324-336">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="1e324-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1e324-337">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1e324-338">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1e324-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="1e324-339">Linux</span><span class="sxs-lookup"><span data-stu-id="1e324-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="1e324-340">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="1e324-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1e324-341">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="1e324-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1e324-342">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1e324-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e324-343">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="1e324-344">Configuração</span><span class="sxs-lookup"><span data-stu-id="1e324-344">Configuration</span></span>

<span data-ttu-id="1e324-345">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="1e324-346">Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="1e324-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="1e324-347">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="1e324-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="1e324-348">Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e324-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="1e324-349">Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e324-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="1e324-350">Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.</span><span class="sxs-lookup"><span data-stu-id="1e324-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="1e324-351">Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1e324-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1e324-352">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e324-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="1e324-353">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="1e324-354">Opções</span><span class="sxs-lookup"><span data-stu-id="1e324-354">Options</span></span>

<span data-ttu-id="1e324-355">Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="1e324-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="1e324-356">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1e324-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="1e324-357">Por exemplo, o código a seguir define as opções de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="1e324-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="1e324-358">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="1e324-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="1e324-359">Ambientes</span><span class="sxs-lookup"><span data-stu-id="1e324-359">Environments</span></span>

<span data-ttu-id="1e324-360">Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e324-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="1e324-361">Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="1e324-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="1e324-362">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="1e324-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="1e324-363">O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="1e324-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="1e324-364">O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="1e324-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="1e324-365">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1e324-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="1e324-366">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="1e324-366">Logging</span></span>

<span data-ttu-id="1e324-367">O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros.</span><span class="sxs-lookup"><span data-stu-id="1e324-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1e324-368">Provedores disponíveis incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="1e324-368">Available providers include the following:</span></span>

* <span data-ttu-id="1e324-369">Console</span><span class="sxs-lookup"><span data-stu-id="1e324-369">Console</span></span>
* <span data-ttu-id="1e324-370">Depurar</span><span class="sxs-lookup"><span data-stu-id="1e324-370">Debug</span></span>
* <span data-ttu-id="1e324-371">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="1e324-372">Log de eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="1e324-372">Windows Event Log</span></span>
* <span data-ttu-id="1e324-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1e324-373">TraceSource</span></span>
* <span data-ttu-id="1e324-374">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="1e324-374">Azure App Service</span></span>
* <span data-ttu-id="1e324-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1e324-375">Azure Application Insights</span></span>

<span data-ttu-id="1e324-376">Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.</span><span class="sxs-lookup"><span data-stu-id="1e324-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="1e324-377">Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.</span><span class="sxs-lookup"><span data-stu-id="1e324-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="1e324-378">A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log.</span><span class="sxs-lookup"><span data-stu-id="1e324-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="1e324-379">Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="1e324-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="1e324-380">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1e324-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1e324-381">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1e324-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="1e324-382">Roteamento</span><span class="sxs-lookup"><span data-stu-id="1e324-382">Routing</span></span>

<span data-ttu-id="1e324-383">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="1e324-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="1e324-384">O manipulador é normalmente uma Razor página, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="1e324-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="1e324-385">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1e324-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="1e324-386">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1e324-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="1e324-387">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="1e324-387">Error handling</span></span>

<span data-ttu-id="1e324-388">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="1e324-389">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="1e324-389">A developer exception page</span></span>
* <span data-ttu-id="1e324-390">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="1e324-390">Custom error pages</span></span>
* <span data-ttu-id="1e324-391">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="1e324-391">Static status code pages</span></span>
* <span data-ttu-id="1e324-392">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="1e324-392">Startup exception handling</span></span>

<span data-ttu-id="1e324-393">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="1e324-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="1e324-394">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="1e324-394">Make HTTP requests</span></span>

<span data-ttu-id="1e324-395">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="1e324-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="1e324-396">O alocador:</span><span class="sxs-lookup"><span data-stu-id="1e324-396">The factory:</span></span>

* <span data-ttu-id="1e324-397">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="1e324-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="1e324-398">Por exemplo, um cliente *GitHub* pode ser registrado e configurado para acessar o github.</span><span class="sxs-lookup"><span data-stu-id="1e324-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="1e324-399">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="1e324-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="1e324-400">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="1e324-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="1e324-401">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e324-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="1e324-402">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="1e324-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="1e324-403">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="1e324-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="1e324-404">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="1e324-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="1e324-405">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="1e324-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="1e324-406">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="1e324-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="1e324-407">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="1e324-407">Content root</span></span>

<span data-ttu-id="1e324-408">A raiz do conteúdo é o caminho base para:</span><span class="sxs-lookup"><span data-stu-id="1e324-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="1e324-409">Executável que hospeda o aplicativo (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="1e324-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="1e324-410">Assemblies compilados que compõem o aplicativo (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="1e324-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="1e324-411">Arquivos de conteúdo que não são de código usados pelo aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="1e324-412">Razor arquivos (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="1e324-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="1e324-413">Arquivos de configuração (*. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="1e324-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="1e324-414">Arquivos de dados (*. db*)</span><span class="sxs-lookup"><span data-stu-id="1e324-414">Data files (*.db*)</span></span>
* <span data-ttu-id="1e324-415">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* publicada.</span><span class="sxs-lookup"><span data-stu-id="1e324-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="1e324-416">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="1e324-416">During development:</span></span>

* <span data-ttu-id="1e324-417">A raiz do conteúdo assume como padrão o diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1e324-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="1e324-418">O diretório raiz do projeto é usado para criar:</span><span class="sxs-lookup"><span data-stu-id="1e324-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="1e324-419">Caminho para os arquivos de conteúdo sem código do aplicativo no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1e324-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="1e324-420">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1e324-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="1e324-421">Um caminho de raiz de conteúdo alternativo pode ser especificado ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="1e324-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="1e324-422">Para obter mais informações, consulte <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="1e324-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="1e324-423">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="1e324-423">Web root</span></span>

<span data-ttu-id="1e324-424">A raiz da Web é o caminho base para arquivos de recurso estáticos, não de código e públicos, como:</span><span class="sxs-lookup"><span data-stu-id="1e324-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="1e324-425">Folhas de estilo (*. css*)</span><span class="sxs-lookup"><span data-stu-id="1e324-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="1e324-426">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="1e324-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="1e324-427">Imagens (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="1e324-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="1e324-428">Os arquivos estáticos são servidos apenas por padrão no diretório raiz da Web (e subdiretórios).</span><span class="sxs-lookup"><span data-stu-id="1e324-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="1e324-429">O caminho raiz da Web tem como padrão *{Content root}/wwwroot*, mas uma raiz da Web diferente pode ser especificada ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="1e324-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="1e324-430">Para obter mais informações, confira [Diretório base](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="1e324-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="1e324-431">Impedir a publicação de arquivos em *wwwroot* com o [ \<Content> item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="1e324-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="1e324-432">O exemplo a seguir impede a publicação de conteúdo no diretório *wwwroot/local* e nos subpastas:</span><span class="sxs-lookup"><span data-stu-id="1e324-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="1e324-433">Em Razor arquivos (*. cshtml*), a barra til ( `~/` ) aponta para a raiz da Web.</span><span class="sxs-lookup"><span data-stu-id="1e324-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="1e324-434">Um caminho que começa com `~/` é conhecido como um *caminho virtual*.</span><span class="sxs-lookup"><span data-stu-id="1e324-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="1e324-435">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1e324-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
