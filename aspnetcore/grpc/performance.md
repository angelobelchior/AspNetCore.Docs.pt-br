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
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a>Práticas recomendadas de desempenho no gRPC para ASP.NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

o gRPC foi projetado para serviços de alto desempenho. Este documento explica como obter o melhor desempenho possível do gRPC.

## <a name="reuse-channel"></a>Reutilizar canal

Um canal gRPC deve ser reutilizado ao fazer chamadas gRPC. Reutilizar um canal permite que as chamadas sejam multiplexada por meio de uma conexão HTTP/2 existente.

Se um novo canal for criado para cada chamada gRPC, o tempo necessário para concluir pode aumentar significativamente. Cada chamada exigirá várias viagens de ida e volta da rede entre o cliente e o servidor para criar uma conexão HTTP/2:

1. Abrindo um soquete
2. Estabelecendo conexão TCP
3. Negociando o TLS
4. Iniciando conexão HTTP/2
5. Fazendo a chamada gRPC

Os canais são seguros para compartilhar e reutilizar entre chamadas gRPC:

* Os clientes do gRPC são criados com canais. Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.
* Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.
* Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.
* Clientes criados a partir do canal podem fazer várias chamadas simultâneas.

## <a name="connection-concurrency"></a>Simultaneidade de conexão

As conexões HTTP/2 normalmente têm um limite no número [máximo de fluxos simultâneos (solicitações HTTP ativas)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) em uma conexão ao mesmo tempo. Por padrão, a maioria dos servidores define esse limite como 100 fluxos simultâneos.

Um canal do gRPC usa uma única conexão HTTP/2, e as chamadas simultâneas são multiplexada nessa conexão. Quando o número de chamadas ativas atinge o limite de fluxo de conexão, chamadas adicionais são enfileiradas no cliente. As chamadas em fila aguardam que as chamadas ativas sejam concluídas antes de serem enviadas. Aplicativos com alta carga ou chamadas de gRPC de streaming de longa execução podem ver problemas de desempenho causados por chamadas de enfileiramento por causa desse limite.

::: moniker range=">= aspnetcore-5.0"

O .NET 5 apresenta a `SocketsHttpHandler.EnableMultipleHttp2Connections` propriedade. Quando definido como `true` , conexões http/2 adicionais são criadas por um canal quando o limite de fluxo simultâneo é atingido. Quando um `GrpcChannel` é criado, seu interno `SocketsHttpHandler` é configurado automaticamente para criar conexões http/2 adicionais. Se um aplicativo configurar seu próprio manipulador, considere definir `EnableMultipleHttp2Connections` como `true` :

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

Há algumas soluções alternativas para aplicativos .NET Core 3,1:

* Crie canais gRPC separados para áreas do aplicativo com alta carga. Por exemplo, o `Logger` serviço gRPC pode ter uma carga alta. Use um canal separado para criar o `LoggerClient` no aplicativo.
* Use um pool de canais gRPC, por exemplo, crie uma lista de canais gRPC. `Random` é usado para escolher um canal da lista cada vez que um canal de gRPC é necessário. O uso de `Random` distribui aleatoriamente chamadas em várias conexões.

> [!IMPORTANT]
> Aumentar o limite máximo de fluxo simultâneo no servidor é outra maneira de resolver esse problema. No Kestrel, isso é configurado com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .
>
> O aumento do limite de fluxo simultâneo máximo não é recomendado. Um número excessivo de fluxos em uma única conexão HTTP/2 apresenta novos problemas de desempenho:
>
> * Contenção de thread entre fluxos tentando gravar na conexão.
> * A perda de pacotes de conexão faz com que todas as chamadas sejam bloqueadas na camada TCP.

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>Pings de Keep Alive

Os pings de Keep Alive podem ser usados para manter as conexões HTTP/2 ativas durante períodos de inatividade. Ter uma conexão existente de HTTP/2 pronta quando um aplicativo retoma a atividade permite que as chamadas gRPC iniciais sejam feitas rapidamente, sem um atraso causado pela conexão que está sendo restabelecida.

Os pings de Keep Alive são configurados em <xref:System.Net.Http.SocketsHttpHandler> :

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

O código anterior configura um canal que envia um ping de Keep Alive para o servidor a cada 60 segundos durante períodos de inatividade. O ping garante que o servidor e quaisquer proxies em uso não fechem a conexão devido à inatividade.

::: moniker-end

## <a name="streaming"></a>Streaming

o streaming bidirecional gRPC pode ser usado para substituir chamadas gRPC unários em cenários de alto desempenho. Depois que um fluxo bidirecional é iniciado, o streaming de mensagens é mais rápido do que o envio de mensagens com várias chamadas gRPC unários. As mensagens transmitidas são enviadas como dados em uma solicitação HTTP/2 existente e eliminam a sobrecarga de criar uma nova solicitação HTTP/2 para cada chamada unário.

Serviço de exemplo:

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

Cliente de exemplo:

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

A substituição de chamadas unários por meio de streaming bidirecional por motivos de desempenho é uma técnica avançada e não é adequada em muitas situações.

O uso de chamadas de streaming é uma boa opção quando:

1. Alta taxa de transferência ou baixa latência é necessária.
2. gRPC e HTTP/2 são identificados como um afunilamento de desempenho.
3. Um trabalhador no cliente está enviando ou recebendo mensagens regulares com um serviço gRPC.

Esteja ciente da complexidade adicional e das limitações do uso de chamadas de streaming em vez de unário:

1. Um fluxo pode ser interrompido por um erro de serviço ou conexão. A lógica é necessária para reiniciar o fluxo se houver um erro.
2. `RequestStream.WriteAsync` Não é seguro para vários threads. Somente uma mensagem pode ser gravada em um fluxo de cada vez. O envio de mensagens de vários threads em um único fluxo requer uma fila de produtor/consumidor, como <xref:System.Threading.Channels.Channel%601> as mensagens Marshall.
3. Um método de streaming gRPC é limitado ao recebimento de um tipo de mensagem e ao envio de um tipo de mensagem. Por exemplo, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` recebe `RequestMessage` e envia `ResponseMessage` . O suporte do Protobuf para mensagens desconhecidas ou condicionais que usam `Any` e `oneof` pode contornar essa limitação.
