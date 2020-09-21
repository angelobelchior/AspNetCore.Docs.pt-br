---
title: Testar os serviços gRPCs com gRPCurl no ASP.NET Core
author: jamesnk
description: Saiba como testar serviços com ferramentas do gRPC. gRPCurl uma ferramenta de linha de comando para interagir com os serviços do gRPC. gRPCui é uma interface do usuário da Web interativa.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 800b320413552e73f05e0359e67eeb2caf4e0e2a
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770162"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Testar os serviços gRPCs com gRPCurl no ASP.NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

As ferramentas estão disponíveis para gRPC que permitem aos desenvolvedores testar serviços sem criar aplicativos cliente:

* [gRPCurl](https://github.com/fullstorydev/grpcurl) é uma ferramenta de linha de comando que fornece interação com os serviços do gRPC.
* o [gRPCui](https://github.com/fullstorydev/grpcui) se baseia no gRPCurl e adiciona uma interface do usuário da Web interativa para o gRPC, semelhante a ferramentas como o postmaster e a interface do usuário do Swagger.

Este artigo discute como:

* Baixe e instale o gRPCurl e o gRPCui.
* Configure a reflexão do gRPC com um aplicativo gRPC ASP.NET Core.
* Descubra e teste os serviços gRPC com o `grpcurl` .
* Interaja com os serviços gRPCs por meio de um navegador usando o `grpcui` .

## <a name="about-grpcurl"></a>Sobre o gRPCurl

gRPCurl é uma ferramenta de linha de comando criada pela Comunidade do gRPC. Seus recursos incluem:

* Chamar serviços gRPCs, incluindo serviços de streaming.
* Descoberta de serviço usando a [reflexão gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Listar e descrever os serviços gRPCs.
* Funciona com servidores seguros (TLS) e não seguros (texto sem formatação).

Para obter informações sobre como baixar e instalar `grpcurl` o, consulte a [Home Page do GitHub do gRPCurl](https://github.com/fullstorydev/grpcurl#installation).

![linha de comando gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Configurar a reflexão de gRPC

`grpcurl` deve conhecer o contrato de serviços do Protobuf antes de poder chamá-los. Há duas maneiras de fazer isso:

* Configure a [reflexão de gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) no servidor. o gRPCurl descobre automaticamente os contratos de serviço.
* Especifique `.proto` os arquivos nos argumentos de linha de comando para gRPCurl.

É mais fácil usar o gRPCurl com a reflexão do gRPC. a reflexão gRPC adiciona um novo serviço gRPC ao aplicativo que os clientes podem chamar para descobrir serviços.

gRPC ASP.NET Core tem suporte interno para reflexão gRPC com o [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) pacote. Para configurar a reflexão em um aplicativo:

* Adicione uma `Grpc.AspNetCore.Server.Reflection` referência de pacote.
* Registrar reflexão em `Startup.cs` :
  * `AddGrpcReflection` para registrar serviços que habilitam a reflexão.
  * `MapGrpcReflectionService` para adicionar um ponto de extremidade de serviço de reflexão.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Quando a reflexão do gRPC estiver configurada:

* Um serviço de reflexo do gRPC é adicionado ao aplicativo do servidor.
* Os aplicativos cliente que dão suporte à reflexão gRPC podem chamar o serviço de reflexão para descobrir serviços hospedados pelo servidor.
* os serviços gRPCs ainda são chamados do cliente. A reflexão habilita apenas a descoberta de serviço e não ignora a segurança do lado do servidor. Os pontos de extremidade protegidos por [autenticação e autorização](xref:grpc/authn-and-authz) exigem que o chamador transmita as credenciais para que ele seja chamado com êxito.

## <a name="use-grpcurl"></a>Use `grpcurl`.

O `-help` argumento explica `grpcurl` as opções de linha de comando:

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Descobrir serviços

Use o `describe` verbo para exibir os serviços definidos pelo servidor:

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

O exemplo anterior:

* Executa o `describe` verbo no servidor `localhost:5001` .
* Imprime os serviços e os métodos retornados pela reflexão gRPC.
  * `Greeter` é um serviço implementado pelo aplicativo.
  * `ServerReflection` o serviço é adicionado pelo `Grpc.AspNetCore.Server.Reflection` pacote.

Combine `describe` com um serviço, método ou nome de mensagem para exibir seus detalhes:

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Chamar serviços gRPCs

Chame um serviço gRPC especificando um nome de serviço e método junto com um argumento JSON que representa a mensagem de solicitação. O JSON é convertido em Protobuf e enviado para o serviço.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

No exemplo anterior:

* O `-d` argumento especifica uma mensagem de solicitação com JSON. Esse argumento deve vir antes do endereço do servidor e do nome do método.
* Chama o `SayHello` método no `greeter.Greeter` serviço.
* Imprime a mensagem de resposta como JSON.

## <a name="about-grpcui"></a>Sobre o gRPCui

gRPCui é uma interface do usuário da Web interativa para gRPC. Ele se baseia no gRPCurl e oferece uma GUI para descobrir e testar os serviços do gRPC, semelhante às ferramentas HTTP, como o postmaster ou a interface do usuário do Swagger.

Para obter informações sobre como baixar e instalar `grpcui` o, consulte a [Home Page do GitHub do gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Usando `grpcui`

Execute `grpcui` com o endereço do servidor com o qual interagir como um argumento:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

A ferramenta inicia uma janela do navegador com a interface do usuário da Web interativa. os serviços gRPCs são automaticamente descobertos usando a reflexão do gRPC.

![interface do usuário da Web do amgRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Home Page do GitHub gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Home Page do GitHub gRPCui](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
