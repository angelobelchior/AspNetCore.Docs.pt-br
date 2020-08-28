---
title: Criar métodos e serviços gRPCs
author: jamesnk
description: Saiba como criar serviços e métodos do gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: 878792120d69bea9ca6f620a87a7e04da2ec1815
ms.sourcegitcommit: 111b4e451da2e275fb074cde5d8a84b26a81937d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89040834"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="0557c-103">Criar métodos e serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="0557c-103">Create gRPC services and methods</span></span>

<span data-ttu-id="0557c-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0557c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0557c-105">Este documento explica como criar os serviços e métodos do gRPC no C#.</span><span class="sxs-lookup"><span data-stu-id="0557c-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="0557c-106">Os tópicos incluem:</span><span class="sxs-lookup"><span data-stu-id="0557c-106">Topics include:</span></span>

* <span data-ttu-id="0557c-107">Como definir serviços e métodos em arquivos *. proto* .</span><span class="sxs-lookup"><span data-stu-id="0557c-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="0557c-108">Código gerado usando as ferramentas do gRPC C#.</span><span class="sxs-lookup"><span data-stu-id="0557c-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="0557c-109">Implementação de serviços e métodos do gRPC.</span><span class="sxs-lookup"><span data-stu-id="0557c-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="0557c-110">Criar novos serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="0557c-110">Create new gRPC services</span></span>

<span data-ttu-id="0557c-111">os [Serviços gRPCs com C#](xref:grpc/basics) introduziram a abordagem do contrato do gRPC para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="0557c-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="0557c-112">Serviços e mensagens são definidos em arquivos *. proto* .</span><span class="sxs-lookup"><span data-stu-id="0557c-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="0557c-113">As ferramentas C# geram código a partir de arquivos *. proto* .</span><span class="sxs-lookup"><span data-stu-id="0557c-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="0557c-114">Para ativos do lado do servidor, um tipo base abstrato é gerado para cada serviço, juntamente com classes para qualquer mensagem.</span><span class="sxs-lookup"><span data-stu-id="0557c-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="0557c-115">O seguinte arquivo *. proto* :</span><span class="sxs-lookup"><span data-stu-id="0557c-115">The following *.proto* file:</span></span>

* <span data-ttu-id="0557c-116">Define um `Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="0557c-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="0557c-117">O `Greeter` serviço define uma `SayHello` chamada.</span><span class="sxs-lookup"><span data-stu-id="0557c-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="0557c-118">`SayHello` envia uma `HelloRequest` mensagem e recebe uma `HelloReply` mensagem</span><span class="sxs-lookup"><span data-stu-id="0557c-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="0557c-119">As ferramentas c# geram o `GreeterBase` tipo base C#:</span><span class="sxs-lookup"><span data-stu-id="0557c-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="0557c-120">Por padrão, o gerado `GreeterBase` não faz nada.</span><span class="sxs-lookup"><span data-stu-id="0557c-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="0557c-121">Seu `SayHello` método virtual retornará um `UNIMPLEMENTED` erro para todos os clientes que o chamam.</span><span class="sxs-lookup"><span data-stu-id="0557c-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="0557c-122">Para que o serviço seja útil, um aplicativo deve criar uma implementação concreta de `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="0557c-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="0557c-123">A implementação do serviço é registrada com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0557c-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="0557c-124">Se o serviço for hospedado pelo ASP.NET Core gRPC, ele deverá ser adicionado ao pipeline de roteamento com o `MapGrpcService` método.</span><span class="sxs-lookup"><span data-stu-id="0557c-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="0557c-125">Consulte <xref:grpc/aspnetcore> para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="0557c-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="0557c-126">Implementar métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="0557c-126">Implement gRPC methods</span></span>

<span data-ttu-id="0557c-127">Um serviço gRPC pode ter diferentes tipos de métodos.</span><span class="sxs-lookup"><span data-stu-id="0557c-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="0557c-128">A forma como as mensagens são enviadas e recebidas por um serviço depende do tipo de método definido.</span><span class="sxs-lookup"><span data-stu-id="0557c-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="0557c-129">Os tipos de método gRPC são:</span><span class="sxs-lookup"><span data-stu-id="0557c-129">The gRPC method types are:</span></span>

* <span data-ttu-id="0557c-130">Unário</span><span class="sxs-lookup"><span data-stu-id="0557c-130">Unary</span></span>
* <span data-ttu-id="0557c-131">Transmissão de servidor</span><span class="sxs-lookup"><span data-stu-id="0557c-131">Server streaming</span></span>
* <span data-ttu-id="0557c-132">Streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="0557c-132">Client streaming</span></span>
* <span data-ttu-id="0557c-133">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="0557c-133">Bi-directional streaming</span></span>

<span data-ttu-id="0557c-134">As chamadas de streaming são especificadas com a `stream` palavra-chave no arquivo *. proto* .</span><span class="sxs-lookup"><span data-stu-id="0557c-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="0557c-135">`stream` pode ser colocado na mensagem de solicitação de uma chamada, mensagem de resposta ou ambos.</span><span class="sxs-lookup"><span data-stu-id="0557c-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="0557c-136">Cada tipo de chamada tem uma assinatura de método diferente.</span><span class="sxs-lookup"><span data-stu-id="0557c-136">Each call type has a different method signature.</span></span> <span data-ttu-id="0557c-137">Substituir métodos gerados do tipo de serviço base abstrato em uma implementação concreta garante que os argumentos e o tipo de retorno corretos sejam usados.</span><span class="sxs-lookup"><span data-stu-id="0557c-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="0557c-138">Método unário</span><span class="sxs-lookup"><span data-stu-id="0557c-138">Unary method</span></span>

<span data-ttu-id="0557c-139">Um método unário Obtém a mensagem de solicitação como um parâmetro e retorna a resposta.</span><span class="sxs-lookup"><span data-stu-id="0557c-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="0557c-140">Uma chamada unário é concluída quando a resposta é retornada.</span><span class="sxs-lookup"><span data-stu-id="0557c-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="0557c-141">As chamadas unários são as mais semelhantes às [ações em controladores de API da Web](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="0557c-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="0557c-142">Uma diferença importante que os métodos gRPC têm de ações é que os métodos gRPC não são capazes de associar partes de uma solicitação a diferentes argumentos de método.</span><span class="sxs-lookup"><span data-stu-id="0557c-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="0557c-143">os métodos gRPC sempre têm um argumento de mensagem para os dados de solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="0557c-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="0557c-144">Vários valores ainda podem ser enviados para um serviço gRPC, tornando-os campos na mensagem de solicitação:</span><span class="sxs-lookup"><span data-stu-id="0557c-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="0557c-145">Método de transmissão de servidor</span><span class="sxs-lookup"><span data-stu-id="0557c-145">Server streaming method</span></span>

<span data-ttu-id="0557c-146">Um método de transmissão de servidor Obtém a mensagem de solicitação como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0557c-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="0557c-147">Como várias mensagens podem ser transmitidas de volta ao chamador, o `responseStream.WriteAsync` é usado para enviar mensagens de resposta.</span><span class="sxs-lookup"><span data-stu-id="0557c-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="0557c-148">Uma chamada de transmissão de servidor é concluída quando o método retorna.</span><span class="sxs-lookup"><span data-stu-id="0557c-148">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="0557c-149">O cliente não tem como enviar mensagens ou dados adicionais depois que o método de transmissão do servidor for iniciado.</span><span class="sxs-lookup"><span data-stu-id="0557c-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="0557c-150">Alguns métodos de streaming são projetados para serem executados para sempre.</span><span class="sxs-lookup"><span data-stu-id="0557c-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="0557c-151">Para métodos contínuos de streaming, um cliente pode cancelar a chamada quando ela não é mais necessária.</span><span class="sxs-lookup"><span data-stu-id="0557c-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="0557c-152">Quando o cancelamento acontece, o cliente envia um sinal ao servidor e o [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) é gerado.</span><span class="sxs-lookup"><span data-stu-id="0557c-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="0557c-153">O `CancellationToken` token deve ser usado no servidor com métodos assíncronos para que:</span><span class="sxs-lookup"><span data-stu-id="0557c-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="0557c-154">Qualquer trabalho assíncrono é cancelado junto com a chamada de streaming.</span><span class="sxs-lookup"><span data-stu-id="0557c-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="0557c-155">O método é fechado rapidamente.</span><span class="sxs-lookup"><span data-stu-id="0557c-155">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="0557c-156">Método de streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="0557c-156">Client streaming method</span></span>

<span data-ttu-id="0557c-157">Um método de streaming de cliente é iniciado *sem* que o método receba uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="0557c-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="0557c-158">O `requestStream` parâmetro é usado para ler mensagens do cliente.</span><span class="sxs-lookup"><span data-stu-id="0557c-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="0557c-159">Uma chamada de streaming de cliente é concluída quando uma mensagem de resposta é retornada:</span><span class="sxs-lookup"><span data-stu-id="0557c-159">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="0557c-160">Ao usar o C# 8 ou posterior, a `await foreach` sintaxe pode ser usada para ler mensagens.</span><span class="sxs-lookup"><span data-stu-id="0557c-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="0557c-161">O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de solicitação:</span><span class="sxs-lookup"><span data-stu-id="0557c-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="0557c-162">Método de streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="0557c-162">Bi-directional streaming method</span></span>

<span data-ttu-id="0557c-163">Um método de streaming bidirecional é iniciado *sem* o método receber uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="0557c-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="0557c-164">O `requestStream` parâmetro é usado para ler mensagens do cliente.</span><span class="sxs-lookup"><span data-stu-id="0557c-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="0557c-165">O método pode optar por enviar mensagens com `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="0557c-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="0557c-166">Uma chamada de streaming bidirecional é concluída quando o método retorna:</span><span class="sxs-lookup"><span data-stu-id="0557c-166">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="0557c-167">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="0557c-167">The preceding code:</span></span>

* <span data-ttu-id="0557c-168">Envia uma resposta para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="0557c-168">Sends a response for each request.</span></span>
* <span data-ttu-id="0557c-169">É um uso básico de streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="0557c-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="0557c-170">É possível dar suporte a cenários mais complexos, como leitura de solicitações e envio de respostas simultaneamente:</span><span class="sxs-lookup"><span data-stu-id="0557c-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="0557c-171">Em um método de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="0557c-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="0557c-172">A melhor implementação de um método bidirecional varia dependendo dos requisitos.</span><span class="sxs-lookup"><span data-stu-id="0557c-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="0557c-173">Acessar cabeçalhos de solicitação do gRPC</span><span class="sxs-lookup"><span data-stu-id="0557c-173">Access gRPC request headers</span></span>

<span data-ttu-id="0557c-174">Uma mensagem de solicitação não é a única maneira de um cliente enviar dados a um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="0557c-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="0557c-175">Os valores de cabeçalho estão disponíveis em um serviço usando `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="0557c-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="0557c-176">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0557c-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
