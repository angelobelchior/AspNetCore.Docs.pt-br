---
title: Implementação do servidor Web Kestrel no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o Kestrel, o servidor Web multiplataforma do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 50bf2a60f14238c9b71fe90a64c284da202bff59
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491594"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="7ec54-103">Implementação do servidor Web Kestrel no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ec54-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="7ec54-104">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="7ec54-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ec54-105">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="7ec54-106">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="7ec54-107">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="7ec54-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="7ec54-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7ec54-108">HTTPS</span></span>
* <span data-ttu-id="7ec54-109">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="7ec54-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="7ec54-110">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="7ec54-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="7ec54-111">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="7ec54-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="7ec54-112">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7ec54-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="7ec54-113">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="7ec54-114">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ec54-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="7ec54-115">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ec54-115">HTTP/2 support</span></span>

<span data-ttu-id="7ec54-116">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="7ec54-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7ec54-117">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="7ec54-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="7ec54-118">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="7ec54-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="7ec54-119">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="7ec54-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="7ec54-120">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7ec54-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="7ec54-121">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="7ec54-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7ec54-122">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7ec54-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7ec54-123">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7ec54-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="7ec54-124">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="7ec54-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="7ec54-125">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="7ec54-126">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="7ec54-127">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7ec54-128">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="7ec54-129">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="7ec54-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7ec54-130">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="7ec54-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7ec54-131">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7ec54-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7ec54-132">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7ec54-133">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="7ec54-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="7ec54-135">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="7ec54-135">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7ec54-137">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="7ec54-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7ec54-138">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="7ec54-139">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="7ec54-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7ec54-140">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7ec54-141">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7ec54-142">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="7ec54-142">A reverse proxy:</span></span>

* <span data-ttu-id="7ec54-143">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="7ec54-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7ec54-144">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7ec54-145">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7ec54-146">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ec54-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7ec54-147">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="7ec54-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec54-148">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7ec54-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="7ec54-149">KESTREL em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ec54-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="7ec54-150">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="7ec54-151">No *Program.cs*, o <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> método chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="7ec54-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="7ec54-152">Para obter mais informações sobre como criar o host, consulte as seções *configurar um host* e *as configurações do construtor padrão* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="7ec54-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="7ec54-153">Para fornecer configuração adicional após chamar `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="7ec54-154">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ec54-154">Kestrel options</span></span>

<span data-ttu-id="7ec54-155">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="7ec54-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="7ec54-156">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="7ec54-157">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="7ec54-158">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="7ec54-159">Nos exemplos mostrados posteriormente neste artigo, as opções de Kestrel são configuradas no código C#.</span><span class="sxs-lookup"><span data-stu-id="7ec54-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="7ec54-160">As opções de Kestrel também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7ec54-161">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) pode carregar a configuração de Kestrel de um *appsettings.jsem* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7ec54-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="7ec54-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> e a [configuração do ponto de extremidade](#endpoint-configuration) são configuráveis de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="7ec54-163">A configuração restante do Kestrel deve ser configurada no código C#.</span><span class="sxs-lookup"><span data-stu-id="7ec54-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="7ec54-164">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ec54-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="7ec54-165">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7ec54-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="7ec54-166">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7ec54-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="7ec54-167">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="7ec54-168">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="7ec54-169">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="7ec54-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="7ec54-170">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="7ec54-171">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="7ec54-172">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="7ec54-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="7ec54-173">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="7ec54-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="7ec54-174">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="7ec54-175">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="7ec54-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="7ec54-176">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="7ec54-177">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="7ec54-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="7ec54-178">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="7ec54-179">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="7ec54-180">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="7ec54-181">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="7ec54-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="7ec54-182">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="7ec54-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7ec54-183">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="7ec54-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="7ec54-184">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="7ec54-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="7ec54-185">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="7ec54-186">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="7ec54-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7ec54-187">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="7ec54-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="7ec54-188">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="7ec54-189">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="7ec54-190">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="7ec54-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="7ec54-191">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="7ec54-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="7ec54-192">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="7ec54-193">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="7ec54-194">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="7ec54-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="7ec54-195">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ec54-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="7ec54-196">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="7ec54-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="7ec54-197">O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenciado no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é geralmente compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="7ec54-198">No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="7ec54-199">Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="7ec54-200">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="7ec54-201">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="7ec54-202">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="7ec54-203">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="7ec54-204">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-204">Maximum streams per connection</span></span>

<span data-ttu-id="7ec54-205">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="7ec54-206">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="7ec54-207">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="7ec54-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="7ec54-208">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="7ec54-208">Header table size</span></span>

<span data-ttu-id="7ec54-209">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="7ec54-210">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="7ec54-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="7ec54-211">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="7ec54-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="7ec54-212">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="7ec54-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="7ec54-213">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="7ec54-213">Maximum frame size</span></span>

<span data-ttu-id="7ec54-214">`Http2.MaxFrameSize` indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="7ec54-215">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="7ec54-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="7ec54-216">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="7ec54-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="7ec54-217">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-217">Maximum request header size</span></span>

<span data-ttu-id="7ec54-218">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="7ec54-219">Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="7ec54-220">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="7ec54-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="7ec54-221">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="7ec54-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="7ec54-222">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-222">Initial connection window size</span></span>

<span data-ttu-id="7ec54-223">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="7ec54-224">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="7ec54-225">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="7ec54-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="7ec54-226">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="7ec54-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="7ec54-227">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="7ec54-227">Initial stream window size</span></span>

<span data-ttu-id="7ec54-228">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="7ec54-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="7ec54-229">As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="7ec54-230">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="7ec54-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="7ec54-231">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="7ec54-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="7ec54-232">Configuração de ping de Keep Alive do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ec54-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="7ec54-233">O Kestrel pode ser configurado para enviar pings HTTP/2 para clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="7ec54-234">Os pings HTTP/2 atendem a várias finalidades:</span><span class="sxs-lookup"><span data-stu-id="7ec54-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="7ec54-235">Mantenha as conexões ociosas ativas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-235">Keep idle connections alive.</span></span> <span data-ttu-id="7ec54-236">Alguns clientes e servidores proxy fecham as conexões que estão ociosas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="7ec54-237">Os pings HTTP/2 são considerados como atividade em uma conexão e impedem que a conexão seja fechada como ociosa.</span><span class="sxs-lookup"><span data-stu-id="7ec54-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="7ec54-238">Feche as conexões não íntegras.</span><span class="sxs-lookup"><span data-stu-id="7ec54-238">Close unhealthy connections.</span></span> <span data-ttu-id="7ec54-239">As conexões nas quais o cliente não responde ao ping Keep Alive no tempo configurado são fechadas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="7ec54-240">Há duas opções de configuração relacionadas a pings de Keep Alive HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="7ec54-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="7ec54-241">`Http2.KeepAlivePingInterval` é um `TimeSpan` que configura o ping interno.</span><span class="sxs-lookup"><span data-stu-id="7ec54-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="7ec54-242">O servidor enviará um ping de Keep Alive para o cliente se ele não receber nenhum quadro para esse período de tempo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="7ec54-243">Os pings de Keep Alive são desabilitados quando essa opção é definida como `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="7ec54-244">O valor padrão é `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="7ec54-245">`Http2.KeepAlivePingTimeout` é um `TimeSpan` que configura o tempo limite de ping.</span><span class="sxs-lookup"><span data-stu-id="7ec54-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="7ec54-246">Se o servidor não receber nenhum quadro, como um ping de resposta, durante esse tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="7ec54-247">O tempo limite de Keep Alive é desabilitado quando essa opção é definida como `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="7ec54-248">O valor padrão é 20 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="7ec54-249">Trailers</span><span class="sxs-lookup"><span data-stu-id="7ec54-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="7ec54-250">Redefinir</span><span class="sxs-lookup"><span data-stu-id="7ec54-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="7ec54-251">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="7ec54-251">Synchronous I/O</span></span>

<span data-ttu-id="7ec54-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="7ec54-253">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec54-254">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="7ec54-255">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="7ec54-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="7ec54-256">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="7ec54-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="7ec54-257">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="7ec54-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="7ec54-258">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="7ec54-258">Endpoint configuration</span></span>

<span data-ttu-id="7ec54-259">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="7ec54-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="7ec54-260">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="7ec54-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="7ec54-261">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="7ec54-261">Specify URLs using the:</span></span>

* <span data-ttu-id="7ec54-262">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="7ec54-263">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="7ec54-264">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="7ec54-265">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="7ec54-266">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="7ec54-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="7ec54-267">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="7ec54-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="7ec54-268">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ec54-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="7ec54-269">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-269">A development certificate is created:</span></span>

* <span data-ttu-id="7ec54-270">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="7ec54-271">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="7ec54-272">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="7ec54-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="7ec54-273">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7ec54-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7ec54-274">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="7ec54-275">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ec54-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="7ec54-276">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="7ec54-277">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="7ec54-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="7ec54-278">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="7ec54-279">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="7ec54-280">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="7ec54-281">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="7ec54-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="7ec54-282">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="7ec54-283">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="7ec54-284">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="7ec54-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="7ec54-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="7ec54-286">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="7ec54-287">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="7ec54-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="7ec54-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="7ec54-289">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="7ec54-290">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="7ec54-291">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="7ec54-292">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-292">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="7ec54-293">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="7ec54-294">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="7ec54-295">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="7ec54-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="7ec54-296">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="7ec54-297">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="7ec54-298">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="7ec54-299">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="7ec54-300">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="7ec54-301">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="7ec54-302">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="7ec54-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="7ec54-303">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7ec54-304">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="7ec54-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="7ec54-305">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-305">Supported configurations described next:</span></span>

* <span data-ttu-id="7ec54-306">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-306">No configuration</span></span>
* <span data-ttu-id="7ec54-307">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="7ec54-308">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="7ec54-308">Change the defaults in code</span></span>

<span data-ttu-id="7ec54-309">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-309">*No configuration*</span></span>

<span data-ttu-id="7ec54-310">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="7ec54-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="7ec54-311">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="7ec54-312">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="7ec54-313">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="7ec54-314">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="7ec54-315">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="7ec54-316">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="7ec54-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="7ec54-317">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ec54-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="7ec54-318">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="7ec54-319">Por exemplo, o **Certificates**  >  certificado**padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="7ec54-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="7ec54-320">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="7ec54-320">Schema notes:</span></span>

* <span data-ttu-id="7ec54-321">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="7ec54-322">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="7ec54-323">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="7ec54-324">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="7ec54-325">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="7ec54-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="7ec54-326">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="7ec54-327">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ec54-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="7ec54-328">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="7ec54-329">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="7ec54-330">A `Certificate` seção dá suporte **Path**a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="7ec54-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="7ec54-331">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="7ec54-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="7ec54-333">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7ec54-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="7ec54-334">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="7ec54-335">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="7ec54-336">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ec54-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="7ec54-337">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="7ec54-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="7ec54-338">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="7ec54-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="7ec54-339">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="7ec54-340">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="7ec54-340">*Change the defaults in code*</span></span>

<span data-ttu-id="7ec54-341">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="7ec54-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="7ec54-342">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="7ec54-343">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="7ec54-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="7ec54-344">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="7ec54-345">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="7ec54-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="7ec54-346">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="7ec54-347">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="7ec54-348">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="7ec54-349">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="7ec54-349">SNI support requires:</span></span>

* <span data-ttu-id="7ec54-350">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="7ec54-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="7ec54-351">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="7ec54-352">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="7ec54-353">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="7ec54-354">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="7ec54-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="7ec54-355">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-355">Connection logging</span></span>

<span data-ttu-id="7ec54-356">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="7ec54-357">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="7ec54-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="7ec54-358">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="7ec54-359">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="7ec54-360">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="7ec54-360">Bind to a TCP socket</span></span>

<span data-ttu-id="7ec54-361">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="7ec54-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="7ec54-362">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="7ec54-363">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="7ec54-364">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="7ec54-364">Bind to a Unix socket</span></span>

<span data-ttu-id="7ec54-365">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ec54-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="7ec54-366">No arquivo de configuração Nginx, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="7ec54-367">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="7ec54-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="7ec54-368">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="7ec54-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="7ec54-369">Porta 0</span><span class="sxs-lookup"><span data-stu-id="7ec54-369">Port 0</span></span>

<span data-ttu-id="7ec54-370">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="7ec54-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="7ec54-371">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="7ec54-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="7ec54-372">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="7ec54-373">Limitações</span><span class="sxs-lookup"><span data-stu-id="7ec54-373">Limitations</span></span>

<span data-ttu-id="7ec54-374">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ec54-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="7ec54-375">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="7ec54-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="7ec54-376">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="7ec54-376">`urls` host configuration key</span></span>
* <span data-ttu-id="7ec54-377">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="7ec54-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="7ec54-378">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="7ec54-379">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="7ec54-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="7ec54-380">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="7ec54-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="7ec54-381">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="7ec54-382">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="7ec54-382">IIS endpoint configuration</span></span>

<span data-ttu-id="7ec54-383">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="7ec54-384">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7ec54-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="7ec54-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="7ec54-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="7ec54-386">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="7ec54-387">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="7ec54-388">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="7ec54-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="7ec54-389">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="7ec54-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="7ec54-390">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-390">HTTP/1.1 only.</span></span> <span data-ttu-id="7ec54-391">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="7ec54-392">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-392">HTTP/2 only.</span></span> <span data-ttu-id="7ec54-393">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="7ec54-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="7ec54-394">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="7ec54-395">O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7ec54-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="7ec54-396">O `ListenOptions.Protocols` valor padrão para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="7ec54-397">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="7ec54-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="7ec54-398">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7ec54-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="7ec54-399">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="7ec54-399">Renegotiation disabled</span></span>
* <span data-ttu-id="7ec54-400">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="7ec54-400">Compression disabled</span></span>
* <span data-ttu-id="7ec54-401">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="7ec54-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="7ec54-402">Diffie-Hellman de curva elíptica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="7ec54-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="7ec54-403">Diffie-Hellman de campo finito (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="7ec54-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="7ec54-404">Conjunto de codificação não proibido.</span><span class="sxs-lookup"><span data-stu-id="7ec54-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="7ec54-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="7ec54-406">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="7ec54-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="7ec54-407">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="7ec54-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="7ec54-408">Use o middleware de conexão para filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.</span><span class="sxs-lookup"><span data-stu-id="7ec54-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="7ec54-409">O exemplo a seguir gera <xref:System.NotSupportedException> um algoritmo de codificação para o qual o aplicativo não dá suporte.</span><span class="sxs-lookup"><span data-stu-id="7ec54-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="7ec54-410">Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="7ec54-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="7ec54-411">Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="7ec54-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="7ec54-412">A filtragem de conexão também pode ser configurada por meio de um <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="7ec54-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="7ec54-413">No Linux, <xref:System.Net.Security.CipherSuitesPolicy> o pode ser usado para filtrar os Handshakes de TLS em uma base por conexão:</span><span class="sxs-lookup"><span data-stu-id="7ec54-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="7ec54-414">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="7ec54-415">`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="7ec54-416">O seguinte *appsettings.jsno* exemplo estabelece o http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="7ec54-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="7ec54-417">O exemplo a seguir *appsettings.js* estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="7ec54-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="7ec54-418">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="7ec54-419">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="7ec54-419">Transport configuration</span></span>

<span data-ttu-id="7ec54-420">Para projetos que exigem o uso de Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="7ec54-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="7ec54-421">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ec54-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="7ec54-422">Ligue para <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> o `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="7ec54-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="7ec54-423">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="7ec54-423">URL prefixes</span></span>

<span data-ttu-id="7ec54-424">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ec54-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="7ec54-425">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="7ec54-426">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="7ec54-427">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="7ec54-428">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="7ec54-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="7ec54-429">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="7ec54-430">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="7ec54-431">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="7ec54-432">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="7ec54-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="7ec54-433">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="7ec54-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="7ec54-434">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="7ec54-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="7ec54-435">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7ec54-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="7ec54-436">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="7ec54-437">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="7ec54-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="7ec54-438">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="7ec54-439">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="7ec54-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="7ec54-440">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="7ec54-440">Host filtering</span></span>

<span data-ttu-id="7ec54-441">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="7ec54-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="7ec54-442">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="7ec54-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="7ec54-443">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="7ec54-444">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="7ec54-445">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="7ec54-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="7ec54-446">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que é fornecido implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="7ec54-447">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="7ec54-448">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="7ec54-449">Para habilitar o middleware, defina uma `AllowedHosts` chave no *appsettings.jsem* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ec54-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="7ec54-450">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="7ec54-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="7ec54-451">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="7ec54-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="7ec54-452">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="7ec54-453">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ec54-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="7ec54-454">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="7ec54-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="7ec54-455">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="7ec54-456">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7ec54-457">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="7ec54-458">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="7ec54-459">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="7ec54-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="7ec54-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7ec54-460">HTTPS</span></span>
* <span data-ttu-id="7ec54-461">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="7ec54-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="7ec54-462">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="7ec54-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="7ec54-463">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="7ec54-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="7ec54-464">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7ec54-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="7ec54-465">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="7ec54-466">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ec54-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="7ec54-467">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ec54-467">HTTP/2 support</span></span>

<span data-ttu-id="7ec54-468">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="7ec54-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7ec54-469">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="7ec54-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="7ec54-470">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="7ec54-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="7ec54-471">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="7ec54-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="7ec54-472">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7ec54-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="7ec54-473">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="7ec54-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7ec54-474">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7ec54-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7ec54-475">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7ec54-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="7ec54-476">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="7ec54-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="7ec54-477">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="7ec54-478">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="7ec54-479">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7ec54-480">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="7ec54-481">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="7ec54-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7ec54-482">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="7ec54-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7ec54-483">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7ec54-483">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7ec54-484">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7ec54-485">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="7ec54-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="7ec54-487">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="7ec54-487">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7ec54-489">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="7ec54-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7ec54-490">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="7ec54-491">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="7ec54-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7ec54-492">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7ec54-493">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7ec54-494">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="7ec54-494">A reverse proxy:</span></span>

* <span data-ttu-id="7ec54-495">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="7ec54-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7ec54-496">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7ec54-497">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7ec54-498">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ec54-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7ec54-499">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="7ec54-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec54-500">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7ec54-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="7ec54-501">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ec54-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="7ec54-502">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7ec54-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ec54-503">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="7ec54-504">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="7ec54-504">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="7ec54-505">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="7ec54-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="7ec54-506">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="7ec54-507">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="7ec54-508">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ec54-508">Kestrel options</span></span>

<span data-ttu-id="7ec54-509">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="7ec54-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="7ec54-510">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="7ec54-511">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="7ec54-512">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="7ec54-513">As opções de Kestrel, que são configuradas no código C# nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7ec54-514">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração de Kestrel de um *appsettings.jsem* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7ec54-514">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="7ec54-515">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ec54-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="7ec54-516">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7ec54-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="7ec54-517">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7ec54-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="7ec54-518">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="7ec54-519">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="7ec54-520">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="7ec54-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="7ec54-521">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-521">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="7ec54-522">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="7ec54-523">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="7ec54-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="7ec54-524">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="7ec54-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="7ec54-525">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="7ec54-526">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="7ec54-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="7ec54-527">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="7ec54-528">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="7ec54-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="7ec54-529">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="7ec54-530">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="7ec54-531">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="7ec54-532">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="7ec54-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="7ec54-533">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="7ec54-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7ec54-534">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="7ec54-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="7ec54-535">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="7ec54-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="7ec54-536">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="7ec54-537">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="7ec54-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7ec54-538">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="7ec54-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="7ec54-539">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="7ec54-540">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="7ec54-541">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="7ec54-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="7ec54-542">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="7ec54-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="7ec54-543">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="7ec54-544">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="7ec54-545">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="7ec54-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="7ec54-546">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ec54-546">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="7ec54-547">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="7ec54-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="7ec54-548">Nenhum desses recursos de taxa referenciados no exemplo anterior está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="7ec54-549">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="7ec54-550">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="7ec54-551">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="7ec54-552">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="7ec54-553">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-553">Maximum streams per connection</span></span>

<span data-ttu-id="7ec54-554">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="7ec54-555">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="7ec54-556">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="7ec54-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="7ec54-557">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="7ec54-557">Header table size</span></span>

<span data-ttu-id="7ec54-558">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="7ec54-559">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="7ec54-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="7ec54-560">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="7ec54-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="7ec54-561">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="7ec54-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="7ec54-562">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="7ec54-562">Maximum frame size</span></span>

<span data-ttu-id="7ec54-563">O `Http2.MaxFrameSize` indica o tamanho máximo do payload do quadro da conexão HTTP/2 a ser recebido.</span><span class="sxs-lookup"><span data-stu-id="7ec54-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="7ec54-564">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="7ec54-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="7ec54-565">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="7ec54-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="7ec54-566">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-566">Maximum request header size</span></span>

<span data-ttu-id="7ec54-567">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="7ec54-568">Esse limite se aplica ao nome e ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="7ec54-569">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="7ec54-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="7ec54-570">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="7ec54-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="7ec54-571">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-571">Initial connection window size</span></span>

<span data-ttu-id="7ec54-572">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="7ec54-573">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="7ec54-574">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="7ec54-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="7ec54-575">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="7ec54-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="7ec54-576">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="7ec54-576">Initial stream window size</span></span>

<span data-ttu-id="7ec54-577">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="7ec54-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="7ec54-578">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="7ec54-579">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="7ec54-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="7ec54-580">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="7ec54-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="7ec54-581">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="7ec54-581">Synchronous I/O</span></span>

<span data-ttu-id="7ec54-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="7ec54-583">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec54-584">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="7ec54-585">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="7ec54-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="7ec54-586">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="7ec54-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="7ec54-587">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="7ec54-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="7ec54-588">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="7ec54-588">Endpoint configuration</span></span>

<span data-ttu-id="7ec54-589">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="7ec54-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="7ec54-590">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="7ec54-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="7ec54-591">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="7ec54-591">Specify URLs using the:</span></span>

* <span data-ttu-id="7ec54-592">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="7ec54-593">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="7ec54-594">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="7ec54-595">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="7ec54-596">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="7ec54-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="7ec54-597">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="7ec54-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="7ec54-598">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ec54-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="7ec54-599">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-599">A development certificate is created:</span></span>

* <span data-ttu-id="7ec54-600">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="7ec54-601">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="7ec54-602">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="7ec54-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="7ec54-603">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7ec54-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7ec54-604">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="7ec54-605">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ec54-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="7ec54-606">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="7ec54-607">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="7ec54-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="7ec54-608">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="7ec54-609">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="7ec54-610">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="7ec54-611">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="7ec54-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="7ec54-612">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="7ec54-613">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="7ec54-614">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="7ec54-615">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="7ec54-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="7ec54-616">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="7ec54-617">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="7ec54-618">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="7ec54-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="7ec54-619">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="7ec54-620">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="7ec54-621">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="7ec54-622">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-622">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="7ec54-623">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="7ec54-624">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="7ec54-625">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="7ec54-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="7ec54-626">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="7ec54-627">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="7ec54-628">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="7ec54-629">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="7ec54-630">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="7ec54-631">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="7ec54-632">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="7ec54-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="7ec54-633">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7ec54-634">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="7ec54-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="7ec54-635">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-635">Supported configurations described next:</span></span>

* <span data-ttu-id="7ec54-636">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-636">No configuration</span></span>
* <span data-ttu-id="7ec54-637">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="7ec54-638">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="7ec54-638">Change the defaults in code</span></span>

<span data-ttu-id="7ec54-639">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-639">*No configuration*</span></span>

<span data-ttu-id="7ec54-640">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="7ec54-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="7ec54-641">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="7ec54-642">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="7ec54-643">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="7ec54-644">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="7ec54-645">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-645">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="7ec54-646">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="7ec54-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="7ec54-647">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ec54-647">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="7ec54-648">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="7ec54-649">Por exemplo, o **Certificates**  >  certificado**padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="7ec54-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="7ec54-650">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="7ec54-650">Schema notes:</span></span>

* <span data-ttu-id="7ec54-651">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="7ec54-652">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="7ec54-653">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="7ec54-654">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="7ec54-655">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="7ec54-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="7ec54-656">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="7ec54-657">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ec54-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="7ec54-658">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="7ec54-659">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="7ec54-660">A `Certificate` seção dá suporte **Path**a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="7ec54-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="7ec54-661">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="7ec54-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="7ec54-663">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7ec54-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="7ec54-664">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="7ec54-665">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="7ec54-666">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ec54-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="7ec54-667">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="7ec54-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="7ec54-668">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="7ec54-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="7ec54-669">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="7ec54-670">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="7ec54-670">*Change the defaults in code*</span></span>

<span data-ttu-id="7ec54-671">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="7ec54-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="7ec54-672">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="7ec54-673">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="7ec54-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="7ec54-674">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="7ec54-675">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="7ec54-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="7ec54-676">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="7ec54-677">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="7ec54-678">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="7ec54-679">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="7ec54-679">SNI support requires:</span></span>

* <span data-ttu-id="7ec54-680">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="7ec54-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="7ec54-681">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="7ec54-682">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="7ec54-683">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="7ec54-684">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="7ec54-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="7ec54-685">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-685">Connection logging</span></span>

<span data-ttu-id="7ec54-686">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="7ec54-687">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="7ec54-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="7ec54-688">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="7ec54-689">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="7ec54-690">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="7ec54-690">Bind to a TCP socket</span></span>

<span data-ttu-id="7ec54-691">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="7ec54-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="7ec54-692">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="7ec54-693">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="7ec54-694">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="7ec54-694">Bind to a Unix socket</span></span>

<span data-ttu-id="7ec54-695">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ec54-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="7ec54-696">No arquivo Nginx confiuguration, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="7ec54-697">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="7ec54-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="7ec54-698">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="7ec54-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="7ec54-699">Porta 0</span><span class="sxs-lookup"><span data-stu-id="7ec54-699">Port 0</span></span>

<span data-ttu-id="7ec54-700">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="7ec54-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="7ec54-701">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="7ec54-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="7ec54-702">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="7ec54-703">Limitações</span><span class="sxs-lookup"><span data-stu-id="7ec54-703">Limitations</span></span>

<span data-ttu-id="7ec54-704">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ec54-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="7ec54-705">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="7ec54-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="7ec54-706">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="7ec54-706">`urls` host configuration key</span></span>
* <span data-ttu-id="7ec54-707">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="7ec54-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="7ec54-708">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="7ec54-709">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="7ec54-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="7ec54-710">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="7ec54-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="7ec54-711">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="7ec54-712">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="7ec54-712">IIS endpoint configuration</span></span>

<span data-ttu-id="7ec54-713">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="7ec54-714">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7ec54-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="7ec54-715">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="7ec54-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="7ec54-716">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="7ec54-717">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="7ec54-718">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="7ec54-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="7ec54-719">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="7ec54-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="7ec54-720">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-720">HTTP/1.1 only.</span></span> <span data-ttu-id="7ec54-721">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="7ec54-722">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-722">HTTP/2 only.</span></span> <span data-ttu-id="7ec54-723">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="7ec54-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="7ec54-724">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="7ec54-725">O HTTP/2 requer uma conexão TLS e [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) ; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7ec54-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="7ec54-726">O protocolo padrão é HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7ec54-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="7ec54-727">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="7ec54-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="7ec54-728">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7ec54-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="7ec54-729">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="7ec54-729">Renegotiation disabled</span></span>
* <span data-ttu-id="7ec54-730">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="7ec54-730">Compression disabled</span></span>
* <span data-ttu-id="7ec54-731">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="7ec54-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="7ec54-732">Diffie-Hellman de curva elíptica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="7ec54-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="7ec54-733">Diffie-Hellman de campo finito (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="7ec54-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="7ec54-734">Conjunto de codificação não bloqueado</span><span class="sxs-lookup"><span data-stu-id="7ec54-734">Cipher suite not blocked</span></span>

<span data-ttu-id="7ec54-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="7ec54-736">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="7ec54-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="7ec54-737">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="7ec54-737">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="7ec54-738">Crie opcionalmente uma implementação do `IConnectionAdapter` para filtrar os handshakes TLS por conexão para codificações específicas:</span><span class="sxs-lookup"><span data-stu-id="7ec54-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="7ec54-739">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="7ec54-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="7ec54-741">No exemplo *appsettings.json* a seguir, um protocolo de conexão padrão (HTTP/1.1 e HTTP/2) é estabelecido para todos os pontos de extremidade do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-741">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="7ec54-742">O arquivo de configuração de exemplo a seguir estabelece um protocolo de conexão para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="7ec54-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="7ec54-743">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="7ec54-744">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="7ec54-744">Transport configuration</span></span>

<span data-ttu-id="7ec54-745">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="7ec54-746">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="7ec54-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="7ec54-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="7ec54-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="7ec54-748">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="7ec54-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="7ec54-749">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="7ec54-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="7ec54-750">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ec54-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="7ec54-751">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="7ec54-752">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="7ec54-752">URL prefixes</span></span>

<span data-ttu-id="7ec54-753">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ec54-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="7ec54-754">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="7ec54-755">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="7ec54-756">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="7ec54-757">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="7ec54-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="7ec54-758">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="7ec54-759">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="7ec54-760">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="7ec54-761">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="7ec54-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="7ec54-762">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="7ec54-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="7ec54-763">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="7ec54-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="7ec54-764">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7ec54-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="7ec54-765">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="7ec54-766">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="7ec54-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="7ec54-767">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="7ec54-768">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="7ec54-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="7ec54-769">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="7ec54-769">Host filtering</span></span>

<span data-ttu-id="7ec54-770">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="7ec54-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="7ec54-771">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="7ec54-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="7ec54-772">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="7ec54-773">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="7ec54-774">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="7ec54-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="7ec54-775">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="7ec54-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="7ec54-776">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="7ec54-777">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="7ec54-778">Para habilitar o middleware, defina uma `AllowedHosts` chave no *appsettings.jsem* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ec54-778">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="7ec54-779">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="7ec54-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="7ec54-780">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="7ec54-780">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="7ec54-781">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="7ec54-782">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ec54-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="7ec54-783">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="7ec54-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="7ec54-784">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="7ec54-785">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7ec54-786">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="7ec54-787">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="7ec54-788">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="7ec54-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="7ec54-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7ec54-789">HTTPS</span></span>
* <span data-ttu-id="7ec54-790">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="7ec54-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="7ec54-791">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="7ec54-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="7ec54-792">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ec54-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="7ec54-793">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ec54-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7ec54-794">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="7ec54-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7ec54-795">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7ec54-795">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7ec54-796">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7ec54-797">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="7ec54-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="7ec54-799">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="7ec54-799">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7ec54-801">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="7ec54-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7ec54-802">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="7ec54-803">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="7ec54-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7ec54-804">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7ec54-805">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7ec54-806">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="7ec54-806">A reverse proxy:</span></span>

* <span data-ttu-id="7ec54-807">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="7ec54-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7ec54-808">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7ec54-809">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7ec54-810">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ec54-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7ec54-811">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="7ec54-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec54-812">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7ec54-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="7ec54-813">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ec54-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="7ec54-814">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7ec54-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ec54-815">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="7ec54-816">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="7ec54-816">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="7ec54-817">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="7ec54-818">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="7ec54-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="7ec54-819">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ec54-819">Kestrel options</span></span>

<span data-ttu-id="7ec54-820">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="7ec54-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="7ec54-821">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="7ec54-822">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="7ec54-823">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="7ec54-824">As opções de Kestrel, que são configuradas no código C# nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7ec54-825">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração de Kestrel de um *appsettings.jsem* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7ec54-825">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="7ec54-826">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ec54-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="7ec54-827">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7ec54-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="7ec54-828">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7ec54-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="7ec54-829">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="7ec54-830">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="7ec54-831">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="7ec54-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="7ec54-832">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="7ec54-832">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="7ec54-833">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ec54-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="7ec54-834">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="7ec54-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="7ec54-835">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="7ec54-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="7ec54-836">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="7ec54-837">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="7ec54-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="7ec54-838">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="7ec54-839">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="7ec54-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="7ec54-840">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="7ec54-841">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="7ec54-842">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="7ec54-843">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="7ec54-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="7ec54-844">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="7ec54-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7ec54-845">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="7ec54-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="7ec54-846">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="7ec54-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="7ec54-847">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="7ec54-848">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="7ec54-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7ec54-849">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="7ec54-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="7ec54-850">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="7ec54-851">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="7ec54-852">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="7ec54-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="7ec54-853">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="7ec54-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="7ec54-854">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="7ec54-855">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="7ec54-856">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="7ec54-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="7ec54-857">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ec54-857">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="7ec54-858">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="7ec54-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="7ec54-859">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="7ec54-860">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="7ec54-861">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="7ec54-861">Synchronous I/O</span></span>

<span data-ttu-id="7ec54-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="7ec54-863">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec54-864">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="7ec54-865">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="7ec54-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="7ec54-866">O exemplo a seguir desabilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="7ec54-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="7ec54-867">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="7ec54-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="7ec54-868">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="7ec54-868">Endpoint configuration</span></span>

<span data-ttu-id="7ec54-869">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="7ec54-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="7ec54-870">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="7ec54-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="7ec54-871">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="7ec54-871">Specify URLs using the:</span></span>

* <span data-ttu-id="7ec54-872">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="7ec54-873">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="7ec54-874">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="7ec54-875">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="7ec54-876">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="7ec54-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="7ec54-877">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="7ec54-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="7ec54-878">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ec54-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="7ec54-879">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-879">A development certificate is created:</span></span>

* <span data-ttu-id="7ec54-880">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="7ec54-881">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="7ec54-882">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="7ec54-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="7ec54-883">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7ec54-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7ec54-884">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="7ec54-885">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ec54-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="7ec54-886">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="7ec54-887">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="7ec54-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="7ec54-888">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="7ec54-889">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="7ec54-890">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="7ec54-891">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="7ec54-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="7ec54-892">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="7ec54-893">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="7ec54-894">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="7ec54-895">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="7ec54-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="7ec54-896">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="7ec54-897">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="7ec54-898">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="7ec54-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="7ec54-899">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="7ec54-900">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="7ec54-901">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="7ec54-902">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-902">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="7ec54-903">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="7ec54-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="7ec54-904">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ec54-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="7ec54-905">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="7ec54-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="7ec54-906">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="7ec54-907">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="7ec54-908">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="7ec54-909">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="7ec54-910">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="7ec54-911">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="7ec54-912">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="7ec54-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="7ec54-913">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7ec54-914">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="7ec54-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="7ec54-915">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-915">Supported configurations described next:</span></span>

* <span data-ttu-id="7ec54-916">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-916">No configuration</span></span>
* <span data-ttu-id="7ec54-917">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="7ec54-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="7ec54-918">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="7ec54-918">Change the defaults in code</span></span>

<span data-ttu-id="7ec54-919">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-919">*No configuration*</span></span>

<span data-ttu-id="7ec54-920">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="7ec54-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="7ec54-921">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="7ec54-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="7ec54-922">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="7ec54-923">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="7ec54-924">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="7ec54-925">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ec54-925">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="7ec54-926">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="7ec54-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="7ec54-927">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ec54-927">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="7ec54-928">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="7ec54-929">Por exemplo, o **Certificates**  >  certificado**padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="7ec54-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="7ec54-930">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="7ec54-930">Schema notes:</span></span>

* <span data-ttu-id="7ec54-931">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="7ec54-932">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="7ec54-933">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="7ec54-934">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="7ec54-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="7ec54-935">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="7ec54-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="7ec54-936">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="7ec54-937">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ec54-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="7ec54-938">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="7ec54-939">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="7ec54-940">A `Certificate` seção dá suporte **Path**a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="7ec54-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="7ec54-941">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="7ec54-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="7ec54-943">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7ec54-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="7ec54-944">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="7ec54-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="7ec54-945">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="7ec54-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="7ec54-946">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ec54-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="7ec54-947">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="7ec54-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="7ec54-948">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="7ec54-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="7ec54-949">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="7ec54-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="7ec54-950">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="7ec54-950">*Change the defaults in code*</span></span>

<span data-ttu-id="7ec54-951">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="7ec54-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="7ec54-952">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="7ec54-953">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="7ec54-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="7ec54-954">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="7ec54-955">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="7ec54-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="7ec54-956">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="7ec54-957">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="7ec54-958">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="7ec54-959">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="7ec54-959">SNI support requires:</span></span>

* <span data-ttu-id="7ec54-960">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="7ec54-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="7ec54-961">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="7ec54-962">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="7ec54-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="7ec54-963">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="7ec54-964">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="7ec54-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="7ec54-965">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="7ec54-965">Connection logging</span></span>

<span data-ttu-id="7ec54-966">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="7ec54-967">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="7ec54-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="7ec54-968">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="7ec54-969">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="7ec54-970">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="7ec54-970">Bind to a TCP socket</span></span>

<span data-ttu-id="7ec54-971">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="7ec54-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="7ec54-972">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="7ec54-973">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="7ec54-974">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="7ec54-974">Bind to a Unix socket</span></span>

<span data-ttu-id="7ec54-975">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ec54-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="7ec54-976">No arquivo Nginx confiuguration, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="7ec54-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="7ec54-977">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="7ec54-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="7ec54-978">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="7ec54-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="7ec54-979">Porta 0</span><span class="sxs-lookup"><span data-stu-id="7ec54-979">Port 0</span></span>

<span data-ttu-id="7ec54-980">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="7ec54-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="7ec54-981">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="7ec54-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="7ec54-982">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="7ec54-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="7ec54-983">Limitações</span><span class="sxs-lookup"><span data-stu-id="7ec54-983">Limitations</span></span>

<span data-ttu-id="7ec54-984">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ec54-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="7ec54-985">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="7ec54-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="7ec54-986">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="7ec54-986">`urls` host configuration key</span></span>
* <span data-ttu-id="7ec54-987">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="7ec54-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="7ec54-988">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ec54-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="7ec54-989">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="7ec54-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="7ec54-990">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="7ec54-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="7ec54-991">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="7ec54-992">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="7ec54-992">IIS endpoint configuration</span></span>

<span data-ttu-id="7ec54-993">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="7ec54-994">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7ec54-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="7ec54-995">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="7ec54-995">Transport configuration</span></span>

<span data-ttu-id="7ec54-996">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="7ec54-997">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="7ec54-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="7ec54-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="7ec54-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="7ec54-999">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="7ec54-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="7ec54-1000">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="7ec54-1001">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="7ec54-1002">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="7ec54-1003">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="7ec54-1003">URL prefixes</span></span>

<span data-ttu-id="7ec54-1004">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="7ec54-1005">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="7ec54-1006">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="7ec54-1007">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="7ec54-1008">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="7ec54-1009">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="7ec54-1010">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="7ec54-1011">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="7ec54-1012">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="7ec54-1013">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="7ec54-1014">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="7ec54-1015">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7ec54-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="7ec54-1016">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="7ec54-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="7ec54-1017">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="7ec54-1018">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="7ec54-1019">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="7ec54-1020">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="7ec54-1020">Host filtering</span></span>

<span data-ttu-id="7ec54-1021">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="7ec54-1022">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="7ec54-1023">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="7ec54-1024">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="7ec54-1025">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="7ec54-1026">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="7ec54-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="7ec54-1027">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="7ec54-1028">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="7ec54-1029">Para habilitar o middleware, defina uma `AllowedHosts` chave no *appsettings.jsem* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1029">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="7ec54-1030">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="7ec54-1031">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1031">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="7ec54-1032">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="7ec54-1033">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="7ec54-1034">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="7ec54-1035">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="7ec54-1036">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="7ec54-1037">Descarga de solicitação HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="7ec54-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="7ec54-1038">A abertura de conexões HTTP é demorada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="7ec54-1039">Para HTTPS, ele também consome muitos recursos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="7ec54-1040">Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="7ec54-1041">Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="7ec54-1042">O aplicativo nem sempre consome o corpo da solicitação, como `POST` solicitações em que o servidor retorna uma resposta de redirecionamento ou 404.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="7ec54-1043">No `POST` – caso de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="7ec54-1044">O cliente pode já ter enviado parte dos `POST` dados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="7ec54-1045">O servidor grava a resposta 301.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="7ec54-1046">A conexão não pode ser usada para uma nova solicitação até que os `POST` dados do corpo da solicitação anterior tenham sido totalmente lidos.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="7ec54-1047">Kestrel tenta drenar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="7ec54-1048">Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="7ec54-1049">O processo de descarga faz um tradoff entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:</span><span class="sxs-lookup"><span data-stu-id="7ec54-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="7ec54-1050">O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="7ec54-1051">Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="7ec54-1052">Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="7ec54-1053">Por exemplo, os clientes podem ter limites de dados restritivos, portanto, limitar os dados carregados pode ser uma prioridade.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="7ec54-1054">Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="7ec54-1055">Há limitações para chamar `Abort` :</span><span class="sxs-lookup"><span data-stu-id="7ec54-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="7ec54-1056">A criação de novas conexões pode ser lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="7ec54-1057">Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="7ec54-1058">`Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="7ec54-1059">Só chame `Abort` quando um problema específico precisar ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="7ec54-1060">Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando `POST` dados ou quando há um bug no código do cliente que causa grandes ou inúmeras solicitações.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="7ec54-1061">Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="7ec54-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="7ec54-1062">Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="7ec54-1063">No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="7ec54-1064">Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="7ec54-1065">O tempo limite de esgotamento de cinco segundos não se aplica.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="7ec54-1066">Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="7ec54-1067">Quadros de dados de corpo de solicitação adicionais são ignorados.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="7ec54-1068">Se possível, é melhor para os clientes utilizarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="7ec54-1069">Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ec54-1070">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ec54-1070">Additional resources</span></span>

* <span data-ttu-id="7ec54-1071">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="7ec54-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="7ec54-1072">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="7ec54-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="7ec54-1073">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="7ec54-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
