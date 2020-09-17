---
title: Chamar os serviços gRPC com o cliente .NET
author: jamesnk
description: Saiba como chamar serviços gRPCs com o cliente .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 6515e87845cc5aa101532c18711d175a73581bee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722703"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="24a23-103">Chamar os serviços gRPC com o cliente .NET</span><span class="sxs-lookup"><span data-stu-id="24a23-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="24a23-104">Uma biblioteca de cliente .NET gRPC está disponível no pacote NuGet [gRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="24a23-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="24a23-105">Este documento explica como:</span><span class="sxs-lookup"><span data-stu-id="24a23-105">This document explains how to:</span></span>

* <span data-ttu-id="24a23-106">Configure um cliente gRPC para chamar os serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="24a23-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="24a23-107">Faça chamadas gRPC para métodos unários, transmissão de servidor, streaming de cliente e streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="24a23-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="24a23-108">Configurar o cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="24a23-108">Configure gRPC client</span></span>

<span data-ttu-id="24a23-109">Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de arquivos \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="24a23-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="24a23-110">O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="24a23-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="24a23-111">Por exemplo, um serviço chamado `Greeter` gera um `GreeterClient` tipo com métodos para chamar o serviço.</span><span class="sxs-lookup"><span data-stu-id="24a23-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="24a23-112">Um cliente gRPC é criado a partir de um canal.</span><span class="sxs-lookup"><span data-stu-id="24a23-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="24a23-113">Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="24a23-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="24a23-114">Um canal representa uma conexão de vida útil longa para um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="24a23-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="24a23-115">Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço.</span><span class="sxs-lookup"><span data-stu-id="24a23-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="24a23-116">Por exemplo, o `HttpClient` usado para fazer chamadas, o tamanho máximo de mensagens de envio e recebimento e o registro em log podem ser especificados `GrpcChannelOptions` e usados com o `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="24a23-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="24a23-117">Para obter uma lista completa de opções, consulte [Opções de configuração do cliente](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="24a23-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="24a23-118">Configurar TLS</span><span class="sxs-lookup"><span data-stu-id="24a23-118">Configure TLS</span></span>

<span data-ttu-id="24a23-119">Um cliente gRPC deve usar a mesma segurança de nível de conexão que o serviço chamado.</span><span class="sxs-lookup"><span data-stu-id="24a23-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="24a23-120">o protocolo TLS do gRPC é configurado quando o canal gRPC é criado.</span><span class="sxs-lookup"><span data-stu-id="24a23-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="24a23-121">Um cliente gRPC gera um erro ao chamar um serviço e a segurança no nível de conexão do canal e do serviço não correspondem.</span><span class="sxs-lookup"><span data-stu-id="24a23-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="24a23-122">Para configurar um canal do gRPC para usar o TLS, verifique se o endereço do servidor começa com `https` .</span><span class="sxs-lookup"><span data-stu-id="24a23-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="24a23-123">Por exemplo, `GrpcChannel.ForAddress("https://localhost:5001")` usa o protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="24a23-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="24a23-124">O canal gRPC negocia automaticamente uma conexão protegida pelo TLS e usa uma conexão segura para fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="24a23-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="24a23-125">o gRPC dá suporte à autenticação de certificado do cliente sobre TLS.</span><span class="sxs-lookup"><span data-stu-id="24a23-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="24a23-126">Para obter informações sobre como configurar certificados de cliente com um canal do gRPC, consulte <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="24a23-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="24a23-127">Para chamar serviços gRPCs não seguros, verifique se o endereço do servidor começa com `http` .</span><span class="sxs-lookup"><span data-stu-id="24a23-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="24a23-128">Por exemplo, `GrpcChannel.ForAddress("http://localhost:5000")` usa o protocolo http.</span><span class="sxs-lookup"><span data-stu-id="24a23-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="24a23-129">No .NET Core 3,1 ou posterior, é necessária configuração adicional para [chamar serviços gRPCs inseguros com o cliente .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="24a23-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="24a23-130">Desempenho do cliente</span><span class="sxs-lookup"><span data-stu-id="24a23-130">Client performance</span></span>

<span data-ttu-id="24a23-131">Desempenho e uso do canal e do cliente:</span><span class="sxs-lookup"><span data-stu-id="24a23-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="24a23-132">A criação de um canal pode ser uma operação cara.</span><span class="sxs-lookup"><span data-stu-id="24a23-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="24a23-133">Reutilizar um canal para chamadas gRPC fornece benefícios de desempenho.</span><span class="sxs-lookup"><span data-stu-id="24a23-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="24a23-134">Os clientes do gRPC são criados com canais.</span><span class="sxs-lookup"><span data-stu-id="24a23-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="24a23-135">Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.</span><span class="sxs-lookup"><span data-stu-id="24a23-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="24a23-136">Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="24a23-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="24a23-137">Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.</span><span class="sxs-lookup"><span data-stu-id="24a23-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="24a23-138">Clientes criados a partir do canal podem fazer várias chamadas simultâneas.</span><span class="sxs-lookup"><span data-stu-id="24a23-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="24a23-139">`GrpcChannel.ForAddress` Não é a única opção para criar um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="24a23-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="24a23-140">Se estiver chamando serviços gRPC de um aplicativo ASP.NET Core, considere a [integração do gRPC Client Factory](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="24a23-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="24a23-141">a integração do gRPC com `HttpClientFactory` oferece uma alternativa centralizada para a criação de clientes do gRPC.</span><span class="sxs-lookup"><span data-stu-id="24a23-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="24a23-142">Não há suporte para a chamada de gRPC sobre HTTP/2 com `Grpc.Net.Client` atualmente no Xamarin.</span><span class="sxs-lookup"><span data-stu-id="24a23-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="24a23-143">Estamos trabalhando para melhorar o suporte a HTTP/2 em uma versão futura do Xamarin.</span><span class="sxs-lookup"><span data-stu-id="24a23-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="24a23-144">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) e [Grpc-Web](xref:grpc/browser) são alternativas viáveis que funcionam hoje.</span><span class="sxs-lookup"><span data-stu-id="24a23-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="24a23-145">Fazer chamadas gRPC</span><span class="sxs-lookup"><span data-stu-id="24a23-145">Make gRPC calls</span></span>

<span data-ttu-id="24a23-146">Uma chamada gRPC é iniciada chamando um método no cliente.</span><span class="sxs-lookup"><span data-stu-id="24a23-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="24a23-147">O cliente gRPC tratará da serialização de mensagens e abordará a chamada gRPC para o serviço correto.</span><span class="sxs-lookup"><span data-stu-id="24a23-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="24a23-148">gRPC tem tipos diferentes de métodos.</span><span class="sxs-lookup"><span data-stu-id="24a23-148">gRPC has different types of methods.</span></span> <span data-ttu-id="24a23-149">Como o cliente é usado para fazer uma chamada gRPC depende do tipo de método chamado.</span><span class="sxs-lookup"><span data-stu-id="24a23-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="24a23-150">Os tipos de método gRPC são:</span><span class="sxs-lookup"><span data-stu-id="24a23-150">The gRPC method types are:</span></span>

* <span data-ttu-id="24a23-151">Unário</span><span class="sxs-lookup"><span data-stu-id="24a23-151">Unary</span></span>
* <span data-ttu-id="24a23-152">Transmissão de servidor</span><span class="sxs-lookup"><span data-stu-id="24a23-152">Server streaming</span></span>
* <span data-ttu-id="24a23-153">Streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="24a23-153">Client streaming</span></span>
* <span data-ttu-id="24a23-154">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="24a23-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="24a23-155">Chamada unário</span><span class="sxs-lookup"><span data-stu-id="24a23-155">Unary call</span></span>

<span data-ttu-id="24a23-156">Uma chamada unário começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="24a23-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="24a23-157">Uma mensagem de resposta é retornada quando o serviço é concluído.</span><span class="sxs-lookup"><span data-stu-id="24a23-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="24a23-158">Cada método de serviço unário no arquivo \* \* . proto\* resultará em dois métodos .net no tipo de cliente concreto gRPC para chamar o método: um método assíncrono e um método de bloqueio.</span><span class="sxs-lookup"><span data-stu-id="24a23-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="24a23-159">Por exemplo, `GreeterClient` há duas maneiras de chamar `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="24a23-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="24a23-160">`GreeterClient.SayHelloAsync` -chama o `Greeter.SayHello` serviço de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="24a23-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="24a23-161">Pode ser esperado.</span><span class="sxs-lookup"><span data-stu-id="24a23-161">Can be awaited.</span></span>
* <span data-ttu-id="24a23-162">`GreeterClient.SayHello` -chama `Greeter.SayHello` serviço e bloqueia até a conclusão.</span><span class="sxs-lookup"><span data-stu-id="24a23-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="24a23-163">Não use em código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="24a23-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="24a23-164">Chamada de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="24a23-164">Server streaming call</span></span>

<span data-ttu-id="24a23-165">Uma chamada de transmissão de servidor inicia com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="24a23-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="24a23-166">`ResponseStream.MoveNext()` lê mensagens transmitidas do serviço.</span><span class="sxs-lookup"><span data-stu-id="24a23-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="24a23-167">A chamada de streaming de servidor é concluída quando `ResponseStream.MoveNext()` retorna `false` .</span><span class="sxs-lookup"><span data-stu-id="24a23-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="24a23-168">Ao usar o C# 8 ou posterior, a `await foreach` sintaxe pode ser usada para ler mensagens.</span><span class="sxs-lookup"><span data-stu-id="24a23-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="24a23-169">O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:</span><span class="sxs-lookup"><span data-stu-id="24a23-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="24a23-170">Chamada de streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="24a23-170">Client streaming call</span></span>

<span data-ttu-id="24a23-171">Uma chamada de streaming de cliente inicia *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="24a23-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="24a23-172">O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="24a23-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="24a23-173">Quando o cliente terminar de enviar mensagens, `RequestStream.CompleteAsync()` deverá ser chamado para notificar o serviço.</span><span class="sxs-lookup"><span data-stu-id="24a23-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="24a23-174">A chamada é concluída quando o serviço retorna uma mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="24a23-174">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="24a23-175">Chamada de streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="24a23-175">Bi-directional streaming call</span></span>

<span data-ttu-id="24a23-176">Uma chamada de streaming bidirecional é iniciada *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="24a23-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="24a23-177">O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="24a23-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="24a23-178">As mensagens transmitidas do serviço são acessíveis com o `ResponseStream.MoveNext()` ou o `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="24a23-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="24a23-179">A chamada de streaming bidirecional é concluída quando o `ResponseStream` não tem mais mensagens.</span><span class="sxs-lookup"><span data-stu-id="24a23-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="24a23-180">Para obter o melhor desempenho e para evitar erros desnecessários no cliente e no serviço, tente concluir as chamadas de streaming bidirecionais normalmente.</span><span class="sxs-lookup"><span data-stu-id="24a23-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="24a23-181">Uma chamada bidirecional é concluída normalmente quando o servidor termina de ler o fluxo de solicitação e o cliente concluiu a leitura do fluxo de resposta.</span><span class="sxs-lookup"><span data-stu-id="24a23-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="24a23-182">A chamada de exemplo anterior é um exemplo de uma chamada bidirecional que termina normalmente.</span><span class="sxs-lookup"><span data-stu-id="24a23-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="24a23-183">Na chamada, o cliente:</span><span class="sxs-lookup"><span data-stu-id="24a23-183">In the call, the client:</span></span>

1. <span data-ttu-id="24a23-184">Inicia uma nova chamada de streaming bidirecional chamando `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="24a23-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="24a23-185">Cria uma tarefa em segundo plano para ler mensagens do serviço usando `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="24a23-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="24a23-186">Envia mensagens ao servidor com `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="24a23-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="24a23-187">Notifica o servidor para o qual terminou de enviar mensagens `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="24a23-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="24a23-188">Aguarda até que a tarefa em segundo plano Leia todas as mensagens recebidas.</span><span class="sxs-lookup"><span data-stu-id="24a23-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="24a23-189">Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="24a23-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="24a23-190">A melhor lógica de cliente para interagir com uma chamada bidirecional varia dependendo da lógica do serviço.</span><span class="sxs-lookup"><span data-stu-id="24a23-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="24a23-191">Acesse os trailers gRPC</span><span class="sxs-lookup"><span data-stu-id="24a23-191">Access gRPC trailers</span></span>

<span data-ttu-id="24a23-192">chamadas gRPC podem retornar gRPCs.</span><span class="sxs-lookup"><span data-stu-id="24a23-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="24a23-193">os trailers de gRPC são usados para fornecer metadados de nome/valor sobre uma chamada.</span><span class="sxs-lookup"><span data-stu-id="24a23-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="24a23-194">Os trailers fornecem funcionalidade semelhante aos cabeçalhos HTTP, mas são recebidos no final da chamada.</span><span class="sxs-lookup"><span data-stu-id="24a23-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="24a23-195">os trailers de gRPC são acessíveis usando `GetTrailers()` , que retorna uma coleção de metadados.</span><span class="sxs-lookup"><span data-stu-id="24a23-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="24a23-196">Os trailers são retornados após a conclusão da resposta, portanto, você deve aguardar todas as mensagens de resposta antes de acessar os trailers.</span><span class="sxs-lookup"><span data-stu-id="24a23-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="24a23-197">Chamadas unários e de streaming de cliente devem aguardar `ResponseAsync` antes de chamar `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="24a23-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="24a23-198">As chamadas de streaming de servidor e bidirecional devem terminar de aguardar o fluxo de resposta antes de chamar `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="24a23-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="24a23-199">os trailers de gRPC também podem ser acessados do `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="24a23-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="24a23-200">Um serviço pode retornar os trailers com um status de gRPC não OK.</span><span class="sxs-lookup"><span data-stu-id="24a23-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="24a23-201">Nessa situação, os trailers são recuperados da exceção gerada pelo cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="24a23-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="24a23-202">Configurar prazo</span><span class="sxs-lookup"><span data-stu-id="24a23-202">Configure deadline</span></span>

<span data-ttu-id="24a23-203">A configuração de um prazo de chamada gRPC é recomendada porque fornece um limite superior de quanto tempo uma chamada pode ser executada.</span><span class="sxs-lookup"><span data-stu-id="24a23-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="24a23-204">Ele impede que os serviços de comportamento inadequado executem a execução contínua e esgotando os recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="24a23-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="24a23-205">Os prazos são uma ferramenta útil para a criação de aplicativos confiáveis.</span><span class="sxs-lookup"><span data-stu-id="24a23-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="24a23-206">Configure `CallOptions.Deadline` para definir um prazo para uma chamada gRPC:</span><span class="sxs-lookup"><span data-stu-id="24a23-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="24a23-207">Para obter mais informações, consulte <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="24a23-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24a23-208">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="24a23-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
