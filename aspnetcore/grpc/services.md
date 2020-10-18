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
ms.openlocfilehash: c4d37556a3345d275f45c537a40908c5966fe015
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113615"
---
# <a name="create-grpc-services-and-methods"></a>Criar métodos e serviços gRPCs

Por [James Newton – King](https://twitter.com/jamesnk)

Este documento explica como criar os serviços e métodos do gRPC no C#. Os tópicos incluem:

* Como definir serviços e métodos em arquivos *. proto* .
* Código gerado usando as ferramentas do gRPC C#.
* Implementação de serviços e métodos do gRPC.

## <a name="create-new-grpc-services"></a>Criar novos serviços gRPCs

os [Serviços gRPCs com C#](xref:grpc/basics) introduziram a abordagem do contrato do gRPC para o desenvolvimento de API. Serviços e mensagens são definidos em arquivos *. proto* . As ferramentas C# geram código a partir de arquivos *. proto* . Para ativos do lado do servidor, um tipo base abstrato é gerado para cada serviço, juntamente com classes para qualquer mensagem.

O seguinte arquivo *. proto* :

* Define um `Greeter` serviço.
* O `Greeter` serviço define uma `SayHello` chamada.
* `SayHello` envia uma `HelloRequest` mensagem e recebe uma `HelloReply` mensagem

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

As ferramentas c# geram o `GreeterBase` tipo base C#:

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

Por padrão, o gerado `GreeterBase` não faz nada. Seu `SayHello` método virtual retornará um `UNIMPLEMENTED` erro para todos os clientes que o chamam. Para que o serviço seja útil, um aplicativo deve criar uma implementação concreta de `GreeterBase` :

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

A implementação do serviço é registrada com o aplicativo. Se o serviço for hospedado pelo ASP.NET Core gRPC, ele deverá ser adicionado ao pipeline de roteamento com o `MapGrpcService` método.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Consulte <xref:grpc/aspnetcore> para obter mais informações.

## <a name="implement-grpc-methods"></a>Implementar métodos gRPC

Um serviço gRPC pode ter diferentes tipos de métodos. A forma como as mensagens são enviadas e recebidas por um serviço depende do tipo de método definido. Os tipos de método gRPC são:

* Unário
* Transmissão de servidor
* Streaming de cliente
* Streaming bidirecional

As chamadas de streaming são especificadas com a `stream` palavra-chave no arquivo *. proto* . `stream` pode ser colocado na mensagem de solicitação de uma chamada, mensagem de resposta ou ambos.

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

Cada tipo de chamada tem uma assinatura de método diferente. Substituir métodos gerados do tipo de serviço base abstrato em uma implementação concreta garante que os argumentos e o tipo de retorno corretos sejam usados.

### <a name="unary-method"></a>Método unário

Um método unário Obtém a mensagem de solicitação como um parâmetro e retorna a resposta. Uma chamada unário é concluída quando a resposta é retornada.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

As chamadas unários são as mais semelhantes às [ações em controladores de API da Web](xref:web-api/index). Uma diferença importante que os métodos gRPC têm de ações é que os métodos gRPC não são capazes de associar partes de uma solicitação a diferentes argumentos de método. os métodos gRPC sempre têm um argumento de mensagem para os dados de solicitação de entrada. Vários valores ainda podem ser enviados para um serviço gRPC, tornando-os campos na mensagem de solicitação:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Método de transmissão de servidor

Um método de transmissão de servidor Obtém a mensagem de solicitação como um parâmetro. Como várias mensagens podem ser transmitidas de volta ao chamador, o `responseStream.WriteAsync` é usado para enviar mensagens de resposta. Uma chamada de transmissão de servidor é concluída quando o método retorna.

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

O cliente não tem como enviar mensagens ou dados adicionais depois que o método de transmissão do servidor for iniciado. Alguns métodos de streaming são projetados para serem executados para sempre. Para métodos contínuos de streaming, um cliente pode cancelar a chamada quando ela não é mais necessária. Quando o cancelamento acontece, o cliente envia um sinal ao servidor e o [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) é gerado. O `CancellationToken` token deve ser usado no servidor com métodos assíncronos para que:

* Qualquer trabalho assíncrono é cancelado junto com a chamada de streaming.
* O método é fechado rapidamente.

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

### <a name="client-streaming-method"></a>Método de streaming de cliente

Um método de streaming de cliente é iniciado *sem* que o método receba uma mensagem. O `requestStream` parâmetro é usado para ler mensagens do cliente. Uma chamada de streaming de cliente é concluída quando uma mensagem de resposta é retornada:

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

Ao usar o C# 8 ou posterior, a `await foreach` sintaxe pode ser usada para ler mensagens. O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de solicitação:

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

### <a name="bi-directional-streaming-method"></a>Método de streaming bidirecional

Um método de streaming bidirecional é iniciado *sem* o método receber uma mensagem. O `requestStream` parâmetro é usado para ler mensagens do cliente. O método pode optar por enviar mensagens com `responseStream.WriteAsync` . Uma chamada de streaming bidirecional é concluída quando o método retorna:

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

O código anterior:

* Envia uma resposta para cada solicitação.
* É um uso básico de streaming bidirecional.

É possível dar suporte a cenários mais complexos, como leitura de solicitações e envio de respostas simultaneamente:

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

Em um método de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento. A melhor implementação de um método bidirecional varia dependendo dos requisitos.

## <a name="access-grpc-request-headers"></a>Acessar cabeçalhos de solicitação do gRPC

Uma mensagem de solicitação não é a única maneira de um cliente enviar dados a um serviço gRPC. Os valores de cabeçalho estão disponíveis em um serviço usando `ServerCallContext.RequestHeaders` .

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/basics>
* <xref:grpc/client>
