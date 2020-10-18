---
title: Práticas recomendadas de desempenho com o gRPC
author: jamesnk
description: Conheça as práticas recomendadas para a criação de serviços gRPC de alto desempenho.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: b54d13bfd9207a7b8961c1c4fa9908d3d54a4270
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113849"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="cdada-103">Práticas recomendadas de desempenho com o gRPC</span><span class="sxs-lookup"><span data-stu-id="cdada-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="cdada-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="cdada-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="cdada-105">o gRPC foi projetado para serviços de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="cdada-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="cdada-106">Este documento explica como obter o melhor desempenho possível do gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="cdada-107">Reutilizar canais gRPC</span><span class="sxs-lookup"><span data-stu-id="cdada-107">Reuse gRPC channels</span></span>

<span data-ttu-id="cdada-108">Um canal gRPC deve ser reutilizado ao fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="cdada-109">Reutilizar um canal permite que as chamadas sejam multiplexada por meio de uma conexão HTTP/2 existente.</span><span class="sxs-lookup"><span data-stu-id="cdada-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="cdada-110">Se um novo canal for criado para cada chamada gRPC, o tempo necessário para concluir pode aumentar significativamente.</span><span class="sxs-lookup"><span data-stu-id="cdada-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="cdada-111">Cada chamada exigirá várias viagens de ida e volta da rede entre o cliente e o servidor para criar uma nova conexão HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="cdada-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="cdada-112">Abrindo um soquete</span><span class="sxs-lookup"><span data-stu-id="cdada-112">Opening a socket</span></span>
2. <span data-ttu-id="cdada-113">Estabelecendo conexão TCP</span><span class="sxs-lookup"><span data-stu-id="cdada-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="cdada-114">Negociando o TLS</span><span class="sxs-lookup"><span data-stu-id="cdada-114">Negotiating TLS</span></span>
4. <span data-ttu-id="cdada-115">Iniciando conexão HTTP/2</span><span class="sxs-lookup"><span data-stu-id="cdada-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="cdada-116">Fazendo a chamada gRPC</span><span class="sxs-lookup"><span data-stu-id="cdada-116">Making the gRPC call</span></span>

<span data-ttu-id="cdada-117">Os canais são seguros para compartilhar e reutilizar entre chamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="cdada-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="cdada-118">Os clientes do gRPC são criados com canais.</span><span class="sxs-lookup"><span data-stu-id="cdada-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="cdada-119">Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.</span><span class="sxs-lookup"><span data-stu-id="cdada-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="cdada-120">Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="cdada-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="cdada-121">Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.</span><span class="sxs-lookup"><span data-stu-id="cdada-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="cdada-122">Clientes criados a partir do canal podem fazer várias chamadas simultâneas.</span><span class="sxs-lookup"><span data-stu-id="cdada-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="cdada-123">o gRPC Client Factory oferece uma maneira centralizada de configurar canais.</span><span class="sxs-lookup"><span data-stu-id="cdada-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="cdada-124">Ele reutiliza automaticamente os canais subjacentes.</span><span class="sxs-lookup"><span data-stu-id="cdada-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="cdada-125">Para obter mais informações, consulte <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="cdada-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="cdada-126">Simultaneidade de conexão</span><span class="sxs-lookup"><span data-stu-id="cdada-126">Connection concurrency</span></span>

<span data-ttu-id="cdada-127">As conexões HTTP/2 normalmente têm um limite no número [máximo de fluxos simultâneos (solicitações HTTP ativas)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) em uma conexão ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="cdada-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="cdada-128">Por padrão, a maioria dos servidores define esse limite como 100 fluxos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="cdada-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="cdada-129">Um canal do gRPC usa uma única conexão HTTP/2, e as chamadas simultâneas são multiplexada nessa conexão.</span><span class="sxs-lookup"><span data-stu-id="cdada-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="cdada-130">Quando o número de chamadas ativas atinge o limite de fluxo de conexão, chamadas adicionais são enfileiradas no cliente.</span><span class="sxs-lookup"><span data-stu-id="cdada-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="cdada-131">As chamadas em fila aguardam que as chamadas ativas sejam concluídas antes de serem enviadas.</span><span class="sxs-lookup"><span data-stu-id="cdada-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="cdada-132">Aplicativos com alta carga ou chamadas de gRPC de streaming de longa execução podem ver problemas de desempenho causados por chamadas de enfileiramento por causa desse limite.</span><span class="sxs-lookup"><span data-stu-id="cdada-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cdada-133">O .NET 5 apresenta a `SocketsHttpHandler.EnableMultipleHttp2Connections` propriedade.</span><span class="sxs-lookup"><span data-stu-id="cdada-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="cdada-134">Quando definido como `true` , conexões http/2 adicionais são criadas por um canal quando o limite de fluxo simultâneo é atingido.</span><span class="sxs-lookup"><span data-stu-id="cdada-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="cdada-135">Quando um `GrpcChannel` é criado, seu interno `SocketsHttpHandler` é configurado automaticamente para criar conexões http/2 adicionais.</span><span class="sxs-lookup"><span data-stu-id="cdada-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="cdada-136">Se um aplicativo configurar seu próprio manipulador, considere definir `EnableMultipleHttp2Connections` como `true` :</span><span class="sxs-lookup"><span data-stu-id="cdada-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="cdada-137">Há algumas soluções alternativas para aplicativos .NET Core 3,1:</span><span class="sxs-lookup"><span data-stu-id="cdada-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="cdada-138">Crie canais gRPC separados para áreas do aplicativo com alta carga.</span><span class="sxs-lookup"><span data-stu-id="cdada-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="cdada-139">Por exemplo, o `Logger` serviço gRPC pode ter uma carga alta.</span><span class="sxs-lookup"><span data-stu-id="cdada-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="cdada-140">Use um canal separado para criar o `LoggerClient` no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdada-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="cdada-141">Use um pool de canais gRPC, por exemplo, crie uma lista de canais gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="cdada-142">`Random` é usado para escolher um canal da lista cada vez que um canal de gRPC é necessário.</span><span class="sxs-lookup"><span data-stu-id="cdada-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="cdada-143">O uso de `Random` distribui aleatoriamente chamadas em várias conexões.</span><span class="sxs-lookup"><span data-stu-id="cdada-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cdada-144">Aumentar o limite máximo de fluxo simultâneo no servidor é outra maneira de resolver esse problema.</span><span class="sxs-lookup"><span data-stu-id="cdada-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="cdada-145">No Kestrel, isso é configurado com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="cdada-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="cdada-146">O aumento do limite de fluxo simultâneo máximo não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="cdada-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="cdada-147">Um número excessivo de fluxos em uma única conexão HTTP/2 apresenta novos problemas de desempenho:</span><span class="sxs-lookup"><span data-stu-id="cdada-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="cdada-148">Contenção de thread entre fluxos tentando gravar na conexão.</span><span class="sxs-lookup"><span data-stu-id="cdada-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="cdada-149">A perda de pacotes de conexão faz com que todas as chamadas sejam bloqueadas na camada TCP.</span><span class="sxs-lookup"><span data-stu-id="cdada-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="cdada-150">Balanceamento de carga</span><span class="sxs-lookup"><span data-stu-id="cdada-150">Load balancing</span></span>

<span data-ttu-id="cdada-151">Alguns balanceadores de carga não funcionam com eficiência com o gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="cdada-152">Os balanceadores de carga L4 (transporte) operam em um nível de conexão, distribuindo conexões TCP entre pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="cdada-153">Essa abordagem funciona bem para carregar chamadas de API de balanceamento feitas com HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="cdada-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="cdada-154">As chamadas simultâneas feitas com HTTP/1.1 são enviadas em diferentes conexões, permitindo que as chamadas tenham balanceamento de carga entre pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="cdada-155">Como os balanceadores de carga L4 operam em um nível de conexão, eles não funcionam bem com gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="cdada-156">gRPC usa HTTP/2, que multiplexa várias chamadas em uma única conexão TCP.</span><span class="sxs-lookup"><span data-stu-id="cdada-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="cdada-157">Todas as chamadas de gRPC sobre essa conexão vão para um ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="cdada-158">Há duas opções para balancear a carga com eficiência gRPC:</span><span class="sxs-lookup"><span data-stu-id="cdada-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="cdada-159">Balanceamento de carga no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="cdada-159">Client-side load balancing</span></span>
* <span data-ttu-id="cdada-160">Balanceamento de carga de proxy L7 (aplicativo)</span><span class="sxs-lookup"><span data-stu-id="cdada-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="cdada-161">Somente chamadas gRPC podem ter balanceamento de carga entre pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="cdada-162">Quando uma chamada gRPC de streaming é estabelecida, todas as mensagens enviadas pelo fluxo vão para um ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="cdada-163">Balanceamento de carga no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="cdada-163">Client-side load balancing</span></span>

<span data-ttu-id="cdada-164">Com o balanceamento de carga do lado do cliente, o cliente conhece os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="cdada-165">Para cada chamada de gRPC, ele seleciona um ponto de extremidade diferente para o qual enviar a chamada.</span><span class="sxs-lookup"><span data-stu-id="cdada-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="cdada-166">O balanceamento de carga no lado do cliente é uma boa opção quando a latência é importante.</span><span class="sxs-lookup"><span data-stu-id="cdada-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="cdada-167">Não há nenhum proxy entre o cliente e o serviço para que a chamada seja enviada diretamente ao serviço.</span><span class="sxs-lookup"><span data-stu-id="cdada-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="cdada-168">A desvantagem do balanceamento de carga no lado do cliente é que cada cliente deve manter o controle dos pontos de extremidade disponíveis que ele deve usar.</span><span class="sxs-lookup"><span data-stu-id="cdada-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="cdada-169">O balanceamento de carga de cliente à parte é uma técnica em que o estado de balanceamento de carga é armazenado em um local central.</span><span class="sxs-lookup"><span data-stu-id="cdada-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="cdada-170">Os clientes consultam periodicamente o local central para obter informações a serem usadas ao tomar decisões de balanceamento de carga.</span><span class="sxs-lookup"><span data-stu-id="cdada-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="cdada-171">`Grpc.Net.Client` Atualmente, não dá suporte ao balanceamento de carga do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="cdada-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="cdada-172">O [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) é uma boa opção se o balanceamento de carga do lado do cliente for necessário no .net.</span><span class="sxs-lookup"><span data-stu-id="cdada-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="cdada-173">Balanceamento de carga de proxy</span><span class="sxs-lookup"><span data-stu-id="cdada-173">Proxy load balancing</span></span>

<span data-ttu-id="cdada-174">Um proxy L7 (Application) funciona em um nível mais alto do que um proxy L4 (transporte).</span><span class="sxs-lookup"><span data-stu-id="cdada-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="cdada-175">Os proxies L7 entendem HTTP/2 e são capazes de distribuir chamadas gRPC multiplexada para o proxy em uma conexão HTTP/2 entre vários pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cdada-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="cdada-176">O uso de um proxy é mais simples do que o balanceamento de carga do lado do cliente, mas pode adicionar latência extra a chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="cdada-177">Há muitos proxies L7 disponíveis.</span><span class="sxs-lookup"><span data-stu-id="cdada-177">There are many L7 proxies available.</span></span> <span data-ttu-id="cdada-178">Algumas opções são:</span><span class="sxs-lookup"><span data-stu-id="cdada-178">Some options are:</span></span>

* <span data-ttu-id="cdada-179">[Envoy](https://www.envoyproxy.io/) -um proxy de software livre popular.</span><span class="sxs-lookup"><span data-stu-id="cdada-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="cdada-180">[Linkerd](https://linkerd.io/) -malha de serviço para kubernetes.</span><span class="sxs-lookup"><span data-stu-id="cdada-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="cdada-181">[YARP: um proxy reverso](https://microsoft.github.io/reverse-proxy/) -um proxy de código-fonte aberto de visualização escrito em .net.</span><span class="sxs-lookup"><span data-stu-id="cdada-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="cdada-182">Comunicação entre processos</span><span class="sxs-lookup"><span data-stu-id="cdada-182">Inter-process communication</span></span>

<span data-ttu-id="cdada-183">chamadas de gRPC entre um cliente e um serviço geralmente são enviadas por Soquetes TCP.</span><span class="sxs-lookup"><span data-stu-id="cdada-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="cdada-184">O TCP é ótimo para a comunicação em uma rede, mas a [IPC (comunicação entre processos)](https://wikipedia.org/wiki/Inter-process_communication) é mais eficiente quando o cliente e o serviço estão no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="cdada-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="cdada-185">Considere usar um transporte como soquetes de domínio do UNIX ou pipes nomeados para chamadas gRPC entre processos no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="cdada-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="cdada-186">Para obter mais informações, consulte <xref:grpc/interprocess>.</span><span class="sxs-lookup"><span data-stu-id="cdada-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="cdada-187">Pings de Keep Alive</span><span class="sxs-lookup"><span data-stu-id="cdada-187">Keep alive pings</span></span>

<span data-ttu-id="cdada-188">Os pings de Keep Alive podem ser usados para manter as conexões HTTP/2 ativas durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="cdada-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="cdada-189">Ter uma conexão existente de HTTP/2 pronta quando um aplicativo retoma a atividade permite que as chamadas gRPC iniciais sejam feitas rapidamente, sem um atraso causado pela conexão que está sendo restabelecida.</span><span class="sxs-lookup"><span data-stu-id="cdada-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="cdada-190">Os pings de Keep Alive são configurados em <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="cdada-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="cdada-191">O código anterior configura um canal que envia um ping de Keep Alive para o servidor a cada 60 segundos durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="cdada-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="cdada-192">O ping garante que o servidor e quaisquer proxies em uso não fechem a conexão devido à inatividade.</span><span class="sxs-lookup"><span data-stu-id="cdada-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="cdada-193">Streaming</span><span class="sxs-lookup"><span data-stu-id="cdada-193">Streaming</span></span>

<span data-ttu-id="cdada-194">o streaming bidirecional gRPC pode ser usado para substituir chamadas gRPC unários em cenários de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="cdada-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="cdada-195">Depois que um fluxo bidirecional é iniciado, o streaming de mensagens é mais rápido do que o envio de mensagens com várias chamadas gRPC unários.</span><span class="sxs-lookup"><span data-stu-id="cdada-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="cdada-196">As mensagens transmitidas são enviadas como dados em uma solicitação HTTP/2 existente e eliminam a sobrecarga de criar uma nova solicitação HTTP/2 para cada chamada unário.</span><span class="sxs-lookup"><span data-stu-id="cdada-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="cdada-197">Serviço de exemplo:</span><span class="sxs-lookup"><span data-stu-id="cdada-197">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="cdada-198">Cliente de exemplo:</span><span class="sxs-lookup"><span data-stu-id="cdada-198">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="cdada-199">A substituição de chamadas unários por meio de streaming bidirecional por motivos de desempenho é uma técnica avançada e não é adequada em muitas situações.</span><span class="sxs-lookup"><span data-stu-id="cdada-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="cdada-200">O uso de chamadas de streaming é uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="cdada-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="cdada-201">Alta taxa de transferência ou baixa latência é necessária.</span><span class="sxs-lookup"><span data-stu-id="cdada-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="cdada-202">gRPC e HTTP/2 são identificados como um afunilamento de desempenho.</span><span class="sxs-lookup"><span data-stu-id="cdada-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="cdada-203">Um trabalhador no cliente está enviando ou recebendo mensagens regulares com um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="cdada-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="cdada-204">Esteja ciente da complexidade adicional e das limitações do uso de chamadas de streaming em vez de unário:</span><span class="sxs-lookup"><span data-stu-id="cdada-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="cdada-205">Um fluxo pode ser interrompido por um erro de serviço ou conexão.</span><span class="sxs-lookup"><span data-stu-id="cdada-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="cdada-206">A lógica é necessária para reiniciar o fluxo se houver um erro.</span><span class="sxs-lookup"><span data-stu-id="cdada-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="cdada-207">`RequestStream.WriteAsync` Não é seguro para vários threads.</span><span class="sxs-lookup"><span data-stu-id="cdada-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="cdada-208">Somente uma mensagem pode ser gravada em um fluxo de cada vez.</span><span class="sxs-lookup"><span data-stu-id="cdada-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="cdada-209">O envio de mensagens de vários threads em um único fluxo requer uma fila de produtor/consumidor, como <xref:System.Threading.Channels.Channel%601> as mensagens Marshall.</span><span class="sxs-lookup"><span data-stu-id="cdada-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="cdada-210">Um método de streaming gRPC é limitado ao recebimento de um tipo de mensagem e ao envio de um tipo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="cdada-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="cdada-211">Por exemplo, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` recebe `RequestMessage` e envia `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="cdada-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="cdada-212">O suporte do Protobuf para mensagens desconhecidas ou condicionais que usam `Any` e `oneof` pode contornar essa limitação.</span><span class="sxs-lookup"><span data-stu-id="cdada-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
