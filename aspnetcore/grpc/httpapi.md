---
title: Criar APIs Web JSON de gRPC
author: jamesnk
description: Saiba como criar APIs HTTP JSON para serviços gRPCs.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102884"
---
# <a name="create-json-web-apis-from-grpc"></a>Criar APIs Web JSON de gRPC

Por [James Newton – King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> a API HTTP gRPC é um projeto experimental, não um produto confirmado. Queremos:
>
> * Teste que nossa abordagem para criar APIs Web JSON para serviços gRPCs funciona.
> * Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET.
>
> [Deixe comentários](https://github.com/grpc/grpc-dotnet/issues/167) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.

o gRPC é uma maneira moderna de se comunicar entre aplicativos. o gRPC usa HTTP/2, transmissão, Protobuf e contratos de mensagem para criar serviços de alto desempenho e em tempo real.

Uma limitação com o gRPC não é cada plataforma que pode usá-lo. Os navegadores não dão suporte total a HTTP/2, tornando REST e JSON a maneira primária de obter dados em aplicativos de navegador. Mesmo com os benefícios que o gRPC traz, REST e JSON têm um lugar importante em aplicativos modernos. A criação ***de*** APIs Web gRPC e JSON adiciona sobrecarga indesejada ao desenvolvimento de aplicativos.

Este documento discute como criar APIs Web JSON usando os serviços gRPCs.

## <a name="grpc-http-api"></a>API HTTP gRPC

a API HTTP gRPC é uma extensão experimental para ASP.NET Core que cria APIs JSON de RESTful para serviços gRPCs. Uma vez configurado, a API HTTP gRPC permite que os aplicativos chamem os serviços gRPC com conceitos HTTP familiares:

* Verbos HTTP
* Associação de parâmetro de URL
* Solicitações/respostas JSON

gRPC ainda pode ser usado para chamar serviços.

### <a name="usage"></a>Uso

1. Adicione uma referência de pacote a [Microsoft. AspNetCore. Grpc. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).
1. Registre serviços no *Startup.cs* com `AddGrpcHttpApi` .
1. Adicione arquivos [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) e [Google/API/Annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) ao seu projeto.
1. Anote os métodos gRPC em seus arquivos *. proto* com associações http e rotas:

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

O `SayHello` método gRPC agora pode ser invocado como gRPC + Protobuf e como uma API http:

* Solicitação: `HTTP/1.1 GET /v1/greeter/world`
* Resposta: `{ "message": "Hello world" }`

Os logs de servidor mostram que a chamada HTTP é executada por um serviço gRPC. a API HTTP gRPC mapeia a solicitação HTTP de entrada para uma mensagem gRPC e, em seguida, converte a mensagem de resposta em JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Este é um exemplo básico. Consulte [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) para obter mais opções de personalização.

### <a name="grpc-http-api-vs-grpc-web"></a>API HTTP do gRPC vs gRPC-Web

Tanto a API HTTP do gRPC quanto o gRPC-Web permitem que os serviços gRPC sejam chamados de um navegador. No entanto, a maneira que cada um faz é diferente:

* gRPC-Web permite que aplicativos de navegador chamem serviços gRPC do navegador com o gRPC-Web Client e Protobuf. o gRPC-Web exige que o aplicativo de navegador gere um cliente gRPC e tenha a vantagem de enviar mensagens de Protobuf pequenas e rápidas.
* a API HTTP gRPC permite que aplicativos de navegador chamem serviços gRPC como se fossem APIs RESTful com JSON. O aplicativo de navegador não precisa gerar um cliente gRPC nem saber nada sobre gRPC.

Nenhum cliente gerado é criado para a API HTTP gRPC. O `Greeter` serviço anterior pode ser chamado usando APIs JavaScript do navegador:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Status experimental

a API HTTP gRPC é um experimento. Ele não está completo e não tem suporte. Estamos interessados nessa tecnologia e a capacidade que ela permite que os desenvolvedores de aplicativos criem rapidamente serviços gRPC e JSON ao mesmo tempo. Não há compromisso em concluir a API HTTP gRPC.

Queremos medir o interesse do desenvolvedor na API HTTP do gRPC. Se a API HTTP gRPC é interessante para você, [forneça comentários](https://github.com/grpc/grpc-dotnet/issues/167).

## <a name="grpc-gateway"></a>grpc-gateway

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) é outra tecnologia para criar APIs JSON do RESTful a partir dos serviços do grpc. Ele usa as mesmas anotações *. proto* para mapear conceitos http para serviços gRPCs.

A maior diferença entre o grpc-gateway e a API HTTP gRPC é o grpc-gateway usa a geração de código para criar um servidor de proxy reverso. O proxy reverso converte as chamadas RESTful em gRPC e as envia para o serviço gRPC.

Para a instalação e o uso do grpc-gateway, consulte a [documentação do grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).

## <a name="additional-resources"></a>Recursos adicionais

* [documentação do Google. API. HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
