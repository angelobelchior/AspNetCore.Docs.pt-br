---
title: Práticas recomendadas de desempenho no gRPC para ASP.NET Core
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
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876718"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="d085a-103">Práticas recomendadas de desempenho no gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d085a-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="d085a-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d085a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d085a-105">o gRPC foi projetado para serviços de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="d085a-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="d085a-106">Este documento explica como obter o melhor desempenho possível do gRPC.</span><span class="sxs-lookup"><span data-stu-id="d085a-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="d085a-107">Reutilizar canal</span><span class="sxs-lookup"><span data-stu-id="d085a-107">Reuse channel</span></span>

<span data-ttu-id="d085a-108">Um canal gRPC deve ser reutilizado ao fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="d085a-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="d085a-109">Reutilizar um canal permite que as chamadas sejam multiplexada por meio de uma conexão HTTP/2 existente.</span><span class="sxs-lookup"><span data-stu-id="d085a-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="d085a-110">Se um novo canal for criado para cada chamada gRPC, o tempo necessário para concluir pode aumentar significativamente.</span><span class="sxs-lookup"><span data-stu-id="d085a-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="d085a-111">Cada chamada exigirá várias viagens de ida e volta da rede entre o cliente e o servidor para criar uma conexão HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="d085a-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="d085a-112">Abrindo um soquete</span><span class="sxs-lookup"><span data-stu-id="d085a-112">Opening a socket</span></span>
2. <span data-ttu-id="d085a-113">Estabelecendo conexão TCP</span><span class="sxs-lookup"><span data-stu-id="d085a-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="d085a-114">Negociando o TLS</span><span class="sxs-lookup"><span data-stu-id="d085a-114">Negotiating TLS</span></span>
4. <span data-ttu-id="d085a-115">Iniciando conexão HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d085a-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="d085a-116">Fazendo a chamada gRPC</span><span class="sxs-lookup"><span data-stu-id="d085a-116">Making the gRPC call</span></span>

<span data-ttu-id="d085a-117">Os canais são seguros para compartilhar e reutilizar entre chamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="d085a-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="d085a-118">Os clientes do gRPC são criados com canais.</span><span class="sxs-lookup"><span data-stu-id="d085a-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="d085a-119">Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.</span><span class="sxs-lookup"><span data-stu-id="d085a-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="d085a-120">Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="d085a-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="d085a-121">Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.</span><span class="sxs-lookup"><span data-stu-id="d085a-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="d085a-122">Clientes criados a partir do canal podem fazer várias chamadas simultâneas.</span><span class="sxs-lookup"><span data-stu-id="d085a-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="d085a-123">Simultaneidade de conexão</span><span class="sxs-lookup"><span data-stu-id="d085a-123">Connection concurrency</span></span>

<span data-ttu-id="d085a-124">As conexões HTTP/2 normalmente têm um limite no número [máximo de fluxos simultâneos (solicitações HTTP ativas)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) em uma conexão ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="d085a-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="d085a-125">Por padrão, a maioria dos servidores define esse limite como 100 fluxos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="d085a-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="d085a-126">Um canal do gRPC usa uma única conexão HTTP/2, e as chamadas simultâneas são multiplexada nessa conexão.</span><span class="sxs-lookup"><span data-stu-id="d085a-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="d085a-127">Quando o número de chamadas ativas atinge o limite de fluxo de conexão, chamadas adicionais são enfileiradas no cliente.</span><span class="sxs-lookup"><span data-stu-id="d085a-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="d085a-128">As chamadas em fila aguardam que as chamadas ativas sejam concluídas antes de serem enviadas.</span><span class="sxs-lookup"><span data-stu-id="d085a-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="d085a-129">Aplicativos com alta carga ou chamadas de gRPC de streaming de longa execução podem ver problemas de desempenho causados por chamadas de enfileiramento por causa desse limite.</span><span class="sxs-lookup"><span data-stu-id="d085a-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d085a-130">O .NET 5 apresenta a `SocketsHttpHandler.EnableMultipleHttp2Connections` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d085a-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="d085a-131">Quando definido como `true` , conexões http/2 adicionais são criadas por um canal quando o limite de fluxo simultâneo é atingido.</span><span class="sxs-lookup"><span data-stu-id="d085a-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="d085a-132">Quando um `GrpcChannel` é criado, seu interno `SocketsHttpHandler` é configurado automaticamente para criar conexões http/2 adicionais.</span><span class="sxs-lookup"><span data-stu-id="d085a-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="d085a-133">Se um aplicativo configurar seu próprio manipulador, considere definir `EnableMultipleHttp2Connections` como `true` :</span><span class="sxs-lookup"><span data-stu-id="d085a-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="d085a-134">Há algumas soluções alternativas para aplicativos .NET Core 3,1:</span><span class="sxs-lookup"><span data-stu-id="d085a-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="d085a-135">Crie canais gRPC separados para áreas do aplicativo com alta carga.</span><span class="sxs-lookup"><span data-stu-id="d085a-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="d085a-136">Por exemplo, o `Logger` serviço gRPC pode ter uma carga alta.</span><span class="sxs-lookup"><span data-stu-id="d085a-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="d085a-137">Use um canal separado para criar o `LoggerClient` no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d085a-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="d085a-138">Use um pool de canais gRPC, por exemplo, crie uma lista de canais gRPC.</span><span class="sxs-lookup"><span data-stu-id="d085a-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="d085a-139">`Random` é usado para escolher um canal da lista cada vez que um canal de gRPC é necessário.</span><span class="sxs-lookup"><span data-stu-id="d085a-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="d085a-140">O uso de `Random` distribui aleatoriamente chamadas em várias conexões.</span><span class="sxs-lookup"><span data-stu-id="d085a-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d085a-141">Aumentar o limite máximo de fluxo simultâneo no servidor é outra maneira de resolver esse problema.</span><span class="sxs-lookup"><span data-stu-id="d085a-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="d085a-142">No Kestrel, isso é configurado com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="d085a-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="d085a-143">O aumento do limite de fluxo simultâneo máximo não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="d085a-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="d085a-144">Um número excessivo de fluxos em uma única conexão HTTP/2 apresenta novos problemas de desempenho:</span><span class="sxs-lookup"><span data-stu-id="d085a-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="d085a-145">Contenção de thread entre fluxos tentando gravar na conexão.</span><span class="sxs-lookup"><span data-stu-id="d085a-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="d085a-146">A perda de pacotes de conexão faz com que todas as chamadas sejam bloqueadas na camada TCP.</span><span class="sxs-lookup"><span data-stu-id="d085a-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="d085a-147">Pings de Keep Alive</span><span class="sxs-lookup"><span data-stu-id="d085a-147">Keep alive pings</span></span>

<span data-ttu-id="d085a-148">Os pings de Keep Alive podem ser usados para manter as conexões HTTP/2 ativas durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="d085a-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="d085a-149">Ter uma conexão existente de HTTP/2 pronta quando um aplicativo retoma a atividade permite que as chamadas gRPC iniciais sejam feitas rapidamente, sem um atraso causado pela conexão que está sendo restabelecida.</span><span class="sxs-lookup"><span data-stu-id="d085a-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="d085a-150">Os pings de Keep Alive são configurados em <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="d085a-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="d085a-151">O código anterior configura um canal que envia um ping de Keep Alive para o servidor a cada 60 segundos durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="d085a-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="d085a-152">O ping garante que o servidor e quaisquer proxies em uso não fechem a conexão devido à inatividade.</span><span class="sxs-lookup"><span data-stu-id="d085a-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="d085a-153">Streaming</span><span class="sxs-lookup"><span data-stu-id="d085a-153">Streaming</span></span>

<span data-ttu-id="d085a-154">o streaming bidirecional gRPC pode ser usado para substituir chamadas gRPC unários em cenários de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="d085a-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="d085a-155">Depois que um fluxo bidirecional é iniciado, o streaming de mensagens é mais rápido do que o envio de mensagens com várias chamadas gRPC unários.</span><span class="sxs-lookup"><span data-stu-id="d085a-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="d085a-156">As mensagens transmitidas são enviadas como dados em uma solicitação HTTP/2 existente e eliminam a sobrecarga de criar uma nova solicitação HTTP/2 para cada chamada unário.</span><span class="sxs-lookup"><span data-stu-id="d085a-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="d085a-157">Serviço de exemplo:</span><span class="sxs-lookup"><span data-stu-id="d085a-157">Example service:</span></span>

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

<span data-ttu-id="d085a-158">Cliente de exemplo:</span><span class="sxs-lookup"><span data-stu-id="d085a-158">Example client:</span></span>

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

<span data-ttu-id="d085a-159">A substituição de chamadas unários por meio de streaming bidirecional por motivos de desempenho é uma técnica avançada e não é adequada em muitas situações.</span><span class="sxs-lookup"><span data-stu-id="d085a-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="d085a-160">O uso de chamadas de streaming é uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="d085a-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="d085a-161">Alta taxa de transferência ou baixa latência é necessária.</span><span class="sxs-lookup"><span data-stu-id="d085a-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="d085a-162">gRPC e HTTP/2 são identificados como um afunilamento de desempenho.</span><span class="sxs-lookup"><span data-stu-id="d085a-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="d085a-163">Um trabalhador no cliente está enviando ou recebendo mensagens regulares com um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="d085a-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="d085a-164">Esteja ciente da complexidade adicional e das limitações do uso de chamadas de streaming em vez de unário:</span><span class="sxs-lookup"><span data-stu-id="d085a-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="d085a-165">Um fluxo pode ser interrompido por um erro de serviço ou conexão.</span><span class="sxs-lookup"><span data-stu-id="d085a-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="d085a-166">A lógica é necessária para reiniciar o fluxo se houver um erro.</span><span class="sxs-lookup"><span data-stu-id="d085a-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="d085a-167">`RequestStream.WriteAsync` Não é seguro para vários threads.</span><span class="sxs-lookup"><span data-stu-id="d085a-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="d085a-168">Somente uma mensagem pode ser gravada em um fluxo de cada vez.</span><span class="sxs-lookup"><span data-stu-id="d085a-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="d085a-169">O envio de mensagens de vários threads em um único fluxo requer uma fila de produtor/consumidor, como <xref:System.Threading.Channels.Channel%601> as mensagens Marshall.</span><span class="sxs-lookup"><span data-stu-id="d085a-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="d085a-170">Um método de streaming gRPC é limitado ao recebimento de um tipo de mensagem e ao envio de um tipo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="d085a-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="d085a-171">Por exemplo, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` recebe `RequestMessage` e envia `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="d085a-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="d085a-172">O suporte do Protobuf para mensagens desconhecidas ou condicionais que usam `Any` e `oneof` pode contornar essa limitação.</span><span class="sxs-lookup"><span data-stu-id="d085a-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
