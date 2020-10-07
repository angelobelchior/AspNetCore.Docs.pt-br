---
title: Criar APIs da Web JSON do gRPC
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
ms.openlocfilehash: fa4e7489920338344b78874690e64d4080b5a719
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805577"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="ddfb6-103">Criar APIs da Web JSON do gRPC</span><span class="sxs-lookup"><span data-stu-id="ddfb6-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="ddfb6-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ddfb6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ddfb6-105">a API HTTP gRPC é um projeto experimental, não um produto confirmado.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="ddfb6-106">Queremos:</span><span class="sxs-lookup"><span data-stu-id="ddfb6-106">We want to:</span></span>
>
> * <span data-ttu-id="ddfb6-107">Teste que nossa abordagem para criar APIs Web JSON para serviços gRPCs funciona.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="ddfb6-108">Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="ddfb6-109">[Deixe comentários](https://github.com/grpc/grpc-dotnet/issues/167) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="ddfb6-110">o gRPC é uma maneira moderna de se comunicar entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="ddfb6-111">o gRPC usa HTTP/2, transmissão, Protobuf e contratos de mensagem para criar serviços de alto desempenho e em tempo real.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="ddfb6-112">Uma limitação com o gRPC não é cada plataforma que pode usá-lo.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="ddfb6-113">Os navegadores não dão suporte total a HTTP/2, tornando REST e JSON a maneira primária de obter dados em aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="ddfb6-114">Mesmo com os benefícios que o gRPC traz, REST e JSON têm um lugar importante em aplicativos modernos.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="ddfb6-115">A criação ***de*** APIs Web gRPC e JSON adiciona sobrecarga indesejada ao desenvolvimento de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="ddfb6-116">Este documento discute como criar APIs Web JSON usando os serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="ddfb6-117">API HTTP gRPC</span><span class="sxs-lookup"><span data-stu-id="ddfb6-117">gRPC HTTP API</span></span>

<span data-ttu-id="ddfb6-118">a API HTTP gRPC é uma extensão experimental para ASP.NET Core que cria APIs JSON de RESTful para serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="ddfb6-119">Uma vez configurado, a API HTTP gRPC permite que os aplicativos chamem os serviços gRPC com conceitos HTTP familiares:</span><span class="sxs-lookup"><span data-stu-id="ddfb6-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="ddfb6-120">Verbos HTTP</span><span class="sxs-lookup"><span data-stu-id="ddfb6-120">HTTP verbs</span></span>
* <span data-ttu-id="ddfb6-121">Associação de parâmetro de URL</span><span class="sxs-lookup"><span data-stu-id="ddfb6-121">URL parameter binding</span></span>
* <span data-ttu-id="ddfb6-122">Solicitações/respostas JSON</span><span class="sxs-lookup"><span data-stu-id="ddfb6-122">JSON requests/responses</span></span>

<span data-ttu-id="ddfb6-123">gRPC ainda pode ser usado para chamar serviços.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="ddfb6-124">Uso</span><span class="sxs-lookup"><span data-stu-id="ddfb6-124">Usage</span></span>

1. <span data-ttu-id="ddfb6-125">Adicione uma referência de pacote a [Microsoft. AspNetCore. Grpc. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="ddfb6-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="ddfb6-126">Registre serviços no *Startup.cs* com `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="ddfb6-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="ddfb6-127">Adicione arquivos [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) e [Google/API/Annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="ddfb6-128">Anote os métodos gRPC em seus arquivos *. proto* com associações http e rotas:</span><span class="sxs-lookup"><span data-stu-id="ddfb6-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
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

<span data-ttu-id="ddfb6-129">O `SayHello` método gRPC agora pode ser invocado como gRPC + Protobuf e como uma API http:</span><span class="sxs-lookup"><span data-stu-id="ddfb6-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="ddfb6-130">Solicitação: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="ddfb6-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="ddfb6-131">Resposta: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="ddfb6-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="ddfb6-132">Os logs de servidor mostram que a chamada HTTP é executada por um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="ddfb6-133">a API HTTP gRPC mapeia a solicitação HTTP de entrada para uma mensagem gRPC e, em seguida, converte a mensagem de resposta em JSON.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="ddfb6-134">Este é um exemplo básico.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-134">This is a basic example.</span></span> <span data-ttu-id="ddfb6-135">Consulte [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) para obter mais opções de personalização.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="ddfb6-136">API HTTP do gRPC vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="ddfb6-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="ddfb6-137">Tanto a API HTTP do gRPC quanto o gRPC-Web permitem que os serviços gRPC sejam chamados de um navegador.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="ddfb6-138">No entanto, a maneira que cada um faz é diferente:</span><span class="sxs-lookup"><span data-stu-id="ddfb6-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="ddfb6-139">gRPC-Web permite que aplicativos de navegador chamem serviços gRPC do navegador com o gRPC-Web Client e Protobuf.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="ddfb6-140">o gRPC-Web exige que o aplicativo de navegador gere um cliente gRPC e tenha a vantagem de enviar mensagens de Protobuf pequenas e rápidas.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="ddfb6-141">a API HTTP gRPC permite que aplicativos de navegador chamem serviços gRPC como se fossem APIs RESTful com JSON.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="ddfb6-142">O aplicativo de navegador não precisa gerar um cliente gRPC nem saber nada sobre gRPC.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="ddfb6-143">Nenhum cliente gerado é criado para a API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="ddfb6-144">O `Greeter` serviço anterior pode ser chamado usando APIs JavaScript do navegador:</span><span class="sxs-lookup"><span data-stu-id="ddfb6-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="ddfb6-145">Status experimental</span><span class="sxs-lookup"><span data-stu-id="ddfb6-145">Experimental status</span></span>

<span data-ttu-id="ddfb6-146">a API HTTP gRPC é um experimento.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="ddfb6-147">Ele não está completo e não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="ddfb6-148">Estamos interessados nessa tecnologia e a capacidade que ela permite que os desenvolvedores de aplicativos criem rapidamente serviços gRPC e JSON ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="ddfb6-149">Não há compromisso em concluir a API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="ddfb6-150">Queremos medir o interesse do desenvolvedor na API HTTP do gRPC.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="ddfb6-151">Se a API HTTP gRPC é interessante para você, [forneça comentários](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="ddfb6-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="ddfb6-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="ddfb6-152">grpc-gateway</span></span>

<span data-ttu-id="ddfb6-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) é outra tecnologia para criar APIs JSON do RESTful a partir dos serviços do grpc.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="ddfb6-154">Ele usa as mesmas anotações *. proto* para mapear conceitos http para serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="ddfb6-155">A maior diferença entre o grpc-gateway e a API HTTP gRPC é o grpc-gateway usa a geração de código para criar um servidor de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="ddfb6-156">O proxy reverso converte as chamadas RESTful em gRPC e as envia para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="ddfb6-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="ddfb6-157">Para a instalação e o uso do grpc-gateway, consulte a [documentação do grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span><span class="sxs-lookup"><span data-stu-id="ddfb6-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ddfb6-158">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ddfb6-158">Additional resources</span></span>

* [<span data-ttu-id="ddfb6-159">documentação do Google. API. HttpRule</span><span class="sxs-lookup"><span data-stu-id="ddfb6-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
