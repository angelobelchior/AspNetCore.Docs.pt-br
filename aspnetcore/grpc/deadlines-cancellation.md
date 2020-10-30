---
title: Serviços gRPCs confiáveis com prazos e cancelamento
author: jamesnk
description: Saiba como criar serviços gRPC confiáveis com prazos e cancelamento no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
- appsettings.json
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059917"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Serviços gRPCs confiáveis com prazos e cancelamento

Por [James Newton – King](https://twitter.com/jamesnk)

Prazos e cancelamento são recursos usados por clientes gRPC para anular chamadas em andamento. Este artigo discute por que prazos e cancelamento são importantes e como usá-los em aplicativos .NET gRPC.

## <a name="deadlines"></a>Prazos

Um prazo final permite que um cliente gRPC especifique quanto tempo ele aguardará até que uma chamada seja concluída. Quando um prazo é excedido, a chamada é cancelada. Definir um prazo é importante porque ele fornece um limite superior de quanto tempo uma chamada pode ser executada para. Ele impede que os serviços de comportamento inadequado executem a execução contínua e esgotando os recursos do servidor. Os prazos são uma ferramenta útil para a criação de aplicativos confiáveis e devem ser configurados.

Configuração do prazo:

* Um prazo é configurado usando `CallOptions.Deadline` quando uma chamada é feita.
* Não há nenhum valor de prazo padrão. chamadas gRPC não são limitadas por tempo, a menos que um prazo seja especificado.
* Um prazo final é a hora UTC de quando o prazo é excedido. Por exemplo, `DateTime.UtcNow.AddSeconds(5)` é um prazo de 5 segundos a partir de agora.
* Se uma hora passada ou atual for usada, a chamada excederá imediatamente o prazo.
* O prazo é enviado com a chamada gRPC para o serviço e é acompanhado de forma independente pelo cliente e pelo serviço. É possível que uma chamada gRPC seja concluída em uma máquina, mas no momento em que a resposta foi retornada ao cliente, o prazo foi excedido.

Se um prazo for excedido, o cliente e o serviço terão comportamento diferente:

* O cliente anula imediatamente a solicitação HTTP subjacente e gera um `DeadlineExceeded` erro. O aplicativo cliente pode optar por capturar o erro e exibir uma mensagem de tempo limite para o usuário.
* No servidor, a solicitação HTTP em execução é anulada e [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) é gerado. Embora a solicitação HTTP seja anulada, a chamada gRPC continua a ser executada no servidor até que o método seja concluído. É importante que o token de cancelamento seja passado para métodos assíncronos para que eles sejam cancelados junto com a chamada. Por exemplo, passar um token de cancelamento para consultas de banco de dados assíncrono e solicitações HTTP. Passar um token de cancelamento permite que a chamada cancelada seja concluída rapidamente no servidor e libera recursos para outras chamadas.

Configure `CallOptions.Deadline` para definir um prazo para uma chamada gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Usando `ServerCallContext.CancellationToken` o em um serviço gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Propagando prazos

Quando uma chamada gRPC é feita de um serviço gRPC em execução, o prazo final deve ser propagado. Por exemplo:

1. O aplicativo cliente chama `FrontendService.GetUser` um prazo final.
2. `FrontendService` chama `UserService.GetUser`. O prazo especificado pelo cliente deve ser especificado com a nova chamada gRPC.
3. `UserService.GetUser` recebe o prazo final. O tempo limite foi atingido corretamente se o prazo do aplicativo cliente for excedido.

O contexto de chamada fornece a data limite com `ServerCallContext.Deadline` :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

Propagar manualmente os prazos pode ser trabalhoso. O prazo final precisa ser passado para cada chamada e é fácil de perder acidentalmente. Uma solução automática está disponível com o gRPC Client Factory. Especificando `EnableCallContextPropagation` :

* O propaga automaticamente o data limite e o token de cancelamento para chamadas filhas.
* É uma excelente maneira de garantir que cenários de gRPC complexos e aninhados sempre propaguem o prazo e o cancelamento.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Para obter mais informações, consulte <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Cancelamento

O cancelamento permite que um cliente gRPC cancele chamadas de longa execução que não são mais necessárias. Por exemplo, uma chamada gRPC que transmite atualizações em tempo real é iniciada quando o usuário visita uma página em um site. O fluxo deve ser cancelado quando o usuário navega para fora da página.

Uma chamada gRPC pode ser cancelada no cliente passando um token de cancelamento com [calloptions. CancellationToken](xref:System.Threading.CancellationToken) ou chamando `Dispose` na chamada.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

os serviços gRPCs que podem ser cancelados devem:
* Passe `ServerCallContext.CancellationToken` para métodos assíncronos. O cancelamento de métodos assíncronos permite que a chamada no servidor seja concluída rapidamente.
* Propague o token de cancelamento para chamadas filhas. Propagar o token de cancelamento garante que as chamadas filhas sejam canceladas com seu pai. o [gRPC Client Factory](xref:grpc/clientfactory) e `EnableCallContextPropagation()` propaga automaticamente o token de cancelamento.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/client>
* <xref:grpc/clientfactory>
