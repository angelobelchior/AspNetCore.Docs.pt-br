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
ms.openlocfilehash: 28e4f372e301a673644bfa97763ebc930f2d0ad5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634326"
---
# <a name="call-grpc-services-with-the-net-client"></a>Chamar os serviços gRPC com o cliente .NET

Uma biblioteca de cliente .NET gRPC está disponível no pacote NuGet [gRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) . Este documento explica como:

* Configure um cliente gRPC para chamar os serviços gRPC.
* Faça chamadas gRPC para métodos unários, transmissão de servidor, streaming de cliente e streaming bidirecional.

## <a name="configure-grpc-client"></a>Configurar o cliente gRPC

Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de arquivos * \* . proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo * \* . proto* .

Um cliente gRPC é criado a partir de um canal. Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Um canal representa uma conexão de vida útil longa para um serviço gRPC. Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço. Por exemplo, o `HttpClient` usado para fazer chamadas, o tamanho máximo de mensagens de envio e recebimento e o registro em log podem ser especificados `GrpcChannelOptions` e usados com o `GrpcChannel.ForAddress` . Para obter uma lista completa de opções, consulte [Opções de configuração do cliente](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>Configurar TLS

Um cliente gRPC deve usar a mesma segurança de nível de conexão que o serviço chamado. o protocolo TLS do gRPC é configurado quando o canal gRPC é criado. Um cliente gRPC gera um erro ao chamar um serviço e a segurança no nível de conexão do canal e do serviço não correspondem.

Para configurar um canal do gRPC para usar o TLS, verifique se o endereço do servidor começa com `https` . Por exemplo, `GrpcChannel.ForAddress("https://localhost:5001")` usa o protocolo HTTPS. O canal gRPC automaticamente negotates uma conexão protegida pelo TLS e usa uma conexão segura para fazer chamadas gRPC.

> [!TIP]
> o gRPC dá suporte à autenticação de certificado do cliente sobre TLS. Para obter informações sobre como configurar certificados de cliente com um canal do gRPC, consulte <xref:grpc/authn-and-authz#client-certificate-authentication> .

Para chamar serviços gRPCs não seguros, verifique se o endereço do servidor começa com `http` . Por exemplo, `GrpcChannel.ForAddress("http://localhost:5000")` usa o protocolo http. No .NET Core 3,1 ou posterior, é necessária configuração adicional para [chamar serviços gRPCs inseguros com o cliente .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

### <a name="client-performance"></a>Desempenho do cliente

Desempenho e uso do canal e do cliente:

* A criação de um canal pode ser uma operação cara. Reutilizar um canal para chamadas gRPC fornece benefícios de desempenho.
* Os clientes do gRPC são criados com canais. Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.
* Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.
* Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.
* Clientes criados a partir do canal podem fazer várias chamadas simultâneas.

`GrpcChannel.ForAddress` Não é a única opção para criar um cliente gRPC. Se estiver chamando serviços gRPC de um aplicativo ASP.NET Core, considere a [integração do gRPC Client Factory](xref:grpc/clientfactory). a integração do gRPC com `HttpClientFactory` oferece uma alternativa centralizada para a criação de clientes do gRPC.

> [!NOTE]
> Não há suporte para a chamada de gRPC sobre HTTP/2 com `Grpc.Net.Client` atualmente no Xamarin. Estamos trabalhando para melhorar o suporte a HTTP/2 em uma versão futura do Xamarin. [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) e [Grpc-Web](xref:grpc/browser) são alternativas viáveis que funcionam hoje.

## <a name="make-grpc-calls"></a>Fazer chamadas gRPC

Uma chamada gRPC é iniciada chamando um método no cliente. O cliente gRPC tratará da serialização de mensagens e abordará a chamada gRPC para o serviço correto.

gRPC tem tipos diferentes de métodos. Como o cliente é usado para fazer uma chamada gRPC depende do tipo de método chamado. Os tipos de método gRPC são:

* Unário
* Transmissão de servidor
* Streaming de cliente
* Streaming bidirecional

### <a name="unary-call"></a>Chamada unário

Uma chamada unário começa com o cliente enviando uma mensagem de solicitação. Uma mensagem de resposta é retornada quando o serviço é concluído.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Cada método de serviço unário no arquivo * \* . proto* resultará em dois métodos .net no tipo de cliente concreto gRPC para chamar o método: um método assíncrono e um método de bloqueio. Por exemplo, `GreeterClient` há duas maneiras de chamar `SayHello` :

* `GreeterClient.SayHelloAsync` -chama o `Greeter.SayHello` serviço de forma assíncrona. Pode ser esperado.
* `GreeterClient.SayHello` -chama `Greeter.SayHello` serviço e bloqueia até a conclusão. Não use em código assíncrono.

### <a name="server-streaming-call"></a>Chamada de streaming de servidor

Uma chamada de transmissão de servidor inicia com o cliente enviando uma mensagem de solicitação. `ResponseStream.MoveNext()` lê mensagens transmitidas do serviço. A chamada de streaming de servidor é concluída quando `ResponseStream.MoveNext()` retorna `false` .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Ao usar o C# 8 ou posterior, a `await foreach` sintaxe pode ser usada para ler mensagens. O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Chamada de streaming de cliente

Uma chamada de streaming de cliente inicia *sem* que o cliente envie uma mensagem. O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync` . Quando o cliente terminar de enviar mensagens, `RequestStream.CompleteAsync()` deverá ser chamado para notificar o serviço. A chamada é concluída quando o serviço retorna uma mensagem de resposta.

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

### <a name="bi-directional-streaming-call"></a>Chamada de streaming bidirecional

Uma chamada de streaming bidirecional é iniciada *sem* que o cliente envie uma mensagem. O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync` . As mensagens transmitidas do serviço são acessíveis com o `ResponseStream.MoveNext()` ou o `ResponseStream.ReadAllAsync()` . A chamada de streaming bidirecional é concluída quando o `ResponseStream` não tem mais mensagens.

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

Para obter o melhor desempenho e para evitar erros desnecessários no cliente e no serviço, tente concluir as chamadas de streaming bidirecionais normalmente. Uma chamada bidirecional é concluída normalmente quando o servidor termina de ler o fluxo de solicitação e o cliente concluiu a leitura do fluxo de resposta. A chamada de exemplo anterior é um exemplo de uma chamada bidirecional que termina normalmente. Na chamada, o cliente:

1. Inicia uma nova chamada de streaming bidirecional chamando `EchoClient.Echo` .
2. Cria uma tarefa em segundo plano para ler mensagens do serviço usando `ResponseStream.ReadAllAsync()` .
3. Envia mensagens ao servidor com `RequestStream.WriteAsync` .
4. Notifica o servidor para o qual terminou de enviar mensagens `RequestStream.CompleteAsync()` .
5. Aguarda até que a tarefa em segundo plano Leia todas as mensagens recebidas.

Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento. A melhor lógica de cliente para interagir com uma chamada bidirecional varia dependendo da lógica do serviço.

## <a name="access-grpc-trailers"></a>Acesse os trailers gRPC

chamadas gRPC podem retornar gRPCs. os trailers de gRPC são usados para fornecer metadados de nome/valor sobre uma chamada. Os trailers fornecem funcionalidade semelhante aos cabeçalhos HTTP, mas são recebidos no final da chamada.

os trailers de gRPC são acessíveis usando `GetTrailers()` , que retorna uma coleção de metadados. Os trailers são retornados após a conclusão da resposta, portanto, você deve aguardar todas as mensagens de resposta antes de acessar os trailers.

Chamadas unários e de streaming de cliente devem aguardar `ResponseAsync` antes de chamar `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

As chamadas de streaming de servidor e bidirecional devem terminar de aguardar o fluxo de resposta antes de chamar `GetTrailers()` :

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

os trailers de gRPC também podem ser acessados do `RpcException` . Um serviço pode retornar os trailers com um status de gRPC não OK. Nessa situação, os trailers são recuperados da exceção gerada pelo cliente gRPC:

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
