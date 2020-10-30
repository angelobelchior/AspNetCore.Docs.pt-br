---
title: Testar os serviços gRPCs com gRPCurl no ASP.NET Core
author: jamesnk
description: Saiba como testar serviços com ferramentas do gRPC. gRPCurl uma ferramenta de linha de comando para interagir com os serviços do gRPC. gRPCui é uma interface do usuário da Web interativa.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058786"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="dedd7-105">Testar os serviços gRPCs com gRPCurl no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dedd7-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="dedd7-106">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="dedd7-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="dedd7-107">As ferramentas estão disponíveis para gRPC que permitem aos desenvolvedores testar serviços sem criar aplicativos cliente:</span><span class="sxs-lookup"><span data-stu-id="dedd7-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="dedd7-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) é uma ferramenta de linha de comando que fornece interação com os serviços do gRPC.</span><span class="sxs-lookup"><span data-stu-id="dedd7-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="dedd7-109">o [gRPCui](https://github.com/fullstorydev/grpcui) se baseia no gRPCurl e adiciona uma interface do usuário da Web interativa para o gRPC, semelhante a ferramentas como o postmaster e a interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="dedd7-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="dedd7-110">Este artigo discute como:</span><span class="sxs-lookup"><span data-stu-id="dedd7-110">This article discusses how to:</span></span>

* <span data-ttu-id="dedd7-111">Baixe e instale o gRPCurl e o gRPCui.</span><span class="sxs-lookup"><span data-stu-id="dedd7-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="dedd7-112">Configure a reflexão do gRPC com um aplicativo gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dedd7-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="dedd7-113">Descubra e teste os serviços gRPC com o `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="dedd7-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="dedd7-114">Interaja com os serviços gRPCs por meio de um navegador usando o `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="dedd7-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="dedd7-115">Sobre o gRPCurl</span><span class="sxs-lookup"><span data-stu-id="dedd7-115">About gRPCurl</span></span>

<span data-ttu-id="dedd7-116">gRPCurl é uma ferramenta de linha de comando criada pela Comunidade do gRPC.</span><span class="sxs-lookup"><span data-stu-id="dedd7-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="dedd7-117">Seus recursos incluem:</span><span class="sxs-lookup"><span data-stu-id="dedd7-117">Its features include:</span></span>

* <span data-ttu-id="dedd7-118">Chamar serviços gRPCs, incluindo serviços de streaming.</span><span class="sxs-lookup"><span data-stu-id="dedd7-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="dedd7-119">Descoberta de serviço usando a [reflexão gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="dedd7-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="dedd7-120">Listar e descrever os serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="dedd7-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="dedd7-121">Funciona com servidores seguros (TLS) e não seguros (texto sem formatação).</span><span class="sxs-lookup"><span data-stu-id="dedd7-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="dedd7-122">Para obter informações sobre como baixar e instalar `grpcurl` o, consulte a [Home Page do GitHub do gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="dedd7-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![linha de comando gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="dedd7-124">Configurar a reflexão de gRPC</span><span class="sxs-lookup"><span data-stu-id="dedd7-124">Set up gRPC reflection</span></span>

<span data-ttu-id="dedd7-125">`grpcurl` deve conhecer o contrato de serviços do Protobuf antes de poder chamá-los.</span><span class="sxs-lookup"><span data-stu-id="dedd7-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="dedd7-126">Há duas maneiras de fazer isso:</span><span class="sxs-lookup"><span data-stu-id="dedd7-126">There are two ways to do this:</span></span>

* <span data-ttu-id="dedd7-127">Configure a [reflexão de gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) no servidor.</span><span class="sxs-lookup"><span data-stu-id="dedd7-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="dedd7-128">o gRPCurl descobre automaticamente os contratos de serviço.</span><span class="sxs-lookup"><span data-stu-id="dedd7-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="dedd7-129">Especifique `.proto` os arquivos nos argumentos de linha de comando para gRPCurl.</span><span class="sxs-lookup"><span data-stu-id="dedd7-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="dedd7-130">É mais fácil usar o gRPCurl com a reflexão do gRPC.</span><span class="sxs-lookup"><span data-stu-id="dedd7-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="dedd7-131">a reflexão gRPC adiciona um novo serviço gRPC ao aplicativo que os clientes podem chamar para descobrir serviços.</span><span class="sxs-lookup"><span data-stu-id="dedd7-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="dedd7-132">gRPC ASP.NET Core tem suporte interno para reflexão gRPC com o [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) pacote.</span><span class="sxs-lookup"><span data-stu-id="dedd7-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="dedd7-133">Para configurar a reflexão em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dedd7-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="dedd7-134">Adicione uma `Grpc.AspNetCore.Server.Reflection` referência de pacote.</span><span class="sxs-lookup"><span data-stu-id="dedd7-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="dedd7-135">Registrar reflexão em `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="dedd7-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="dedd7-136">`AddGrpcReflection` para registrar serviços que habilitam a reflexão.</span><span class="sxs-lookup"><span data-stu-id="dedd7-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="dedd7-137">`MapGrpcReflectionService` para adicionar um ponto de extremidade de serviço de reflexão.</span><span class="sxs-lookup"><span data-stu-id="dedd7-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="dedd7-138">Quando a reflexão do gRPC estiver configurada:</span><span class="sxs-lookup"><span data-stu-id="dedd7-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="dedd7-139">Um serviço de reflexo do gRPC é adicionado ao aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="dedd7-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="dedd7-140">Os aplicativos cliente que dão suporte à reflexão gRPC podem chamar o serviço de reflexão para descobrir serviços hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="dedd7-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="dedd7-141">os serviços gRPCs ainda são chamados do cliente.</span><span class="sxs-lookup"><span data-stu-id="dedd7-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="dedd7-142">A reflexão habilita apenas a descoberta de serviço e não ignora a segurança do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="dedd7-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="dedd7-143">Os pontos de extremidade protegidos por [autenticação e autorização](xref:grpc/authn-and-authz) exigem que o chamador transmita as credenciais para que ele seja chamado com êxito.</span><span class="sxs-lookup"><span data-stu-id="dedd7-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="dedd7-144">Use `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="dedd7-144">Use `grpcurl`</span></span>

<span data-ttu-id="dedd7-145">O `-help` argumento explica `grpcurl` as opções de linha de comando:</span><span class="sxs-lookup"><span data-stu-id="dedd7-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="dedd7-146">Descobrir serviços</span><span class="sxs-lookup"><span data-stu-id="dedd7-146">Discover services</span></span>

<span data-ttu-id="dedd7-147">Use o `describe` verbo para exibir os serviços definidos pelo servidor:</span><span class="sxs-lookup"><span data-stu-id="dedd7-147">Use the `describe` verb to view the services defined by the server:</span></span>

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

<span data-ttu-id="dedd7-148">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="dedd7-148">The preceding example:</span></span>

* <span data-ttu-id="dedd7-149">Executa o `describe` verbo no servidor `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="dedd7-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="dedd7-150">Imprime os serviços e os métodos retornados pela reflexão gRPC.</span><span class="sxs-lookup"><span data-stu-id="dedd7-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="dedd7-151">`Greeter` é um serviço implementado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dedd7-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="dedd7-152">`ServerReflection` o serviço é adicionado pelo `Grpc.AspNetCore.Server.Reflection` pacote.</span><span class="sxs-lookup"><span data-stu-id="dedd7-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="dedd7-153">Combine `describe` com um serviço, método ou nome de mensagem para exibir seus detalhes:</span><span class="sxs-lookup"><span data-stu-id="dedd7-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="dedd7-154">Chamar serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="dedd7-154">Call gRPC services</span></span>

<span data-ttu-id="dedd7-155">Chame um serviço gRPC especificando um nome de serviço e método junto com um argumento JSON que representa a mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="dedd7-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="dedd7-156">O JSON é convertido em Protobuf e enviado para o serviço.</span><span class="sxs-lookup"><span data-stu-id="dedd7-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="dedd7-157">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="dedd7-157">In the preceding example:</span></span>

* <span data-ttu-id="dedd7-158">O `-d` argumento especifica uma mensagem de solicitação com JSON.</span><span class="sxs-lookup"><span data-stu-id="dedd7-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="dedd7-159">Esse argumento deve vir antes do endereço do servidor e do nome do método.</span><span class="sxs-lookup"><span data-stu-id="dedd7-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="dedd7-160">Chama o `SayHello` método no `greeter.Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="dedd7-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="dedd7-161">Imprime a mensagem de resposta como JSON.</span><span class="sxs-lookup"><span data-stu-id="dedd7-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="dedd7-162">Sobre o gRPCui</span><span class="sxs-lookup"><span data-stu-id="dedd7-162">About gRPCui</span></span>

<span data-ttu-id="dedd7-163">gRPCui é uma interface do usuário da Web interativa para gRPC.</span><span class="sxs-lookup"><span data-stu-id="dedd7-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="dedd7-164">Ele se baseia no gRPCurl e oferece uma GUI para descobrir e testar os serviços do gRPC, semelhante às ferramentas HTTP, como o postmaster ou a interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="dedd7-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="dedd7-165">Para obter informações sobre como baixar e instalar `grpcui` o, consulte a [Home Page do GitHub do gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="dedd7-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="dedd7-166">Usando `grpcui`</span><span class="sxs-lookup"><span data-stu-id="dedd7-166">Using `grpcui`</span></span>

<span data-ttu-id="dedd7-167">Execute `grpcui` com o endereço do servidor com o qual interagir como um argumento:</span><span class="sxs-lookup"><span data-stu-id="dedd7-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="dedd7-168">A ferramenta inicia uma janela do navegador com a interface do usuário da Web interativa.</span><span class="sxs-lookup"><span data-stu-id="dedd7-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="dedd7-169">os serviços gRPCs são automaticamente descobertos usando a reflexão do gRPC.</span><span class="sxs-lookup"><span data-stu-id="dedd7-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![interface do usuário da Web do amgRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="dedd7-171">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dedd7-171">Additional resources</span></span>

* [<span data-ttu-id="dedd7-172">Home Page do GitHub gRPCurl</span><span class="sxs-lookup"><span data-stu-id="dedd7-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="dedd7-173">Home Page do GitHub gRPCui</span><span class="sxs-lookup"><span data-stu-id="dedd7-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
