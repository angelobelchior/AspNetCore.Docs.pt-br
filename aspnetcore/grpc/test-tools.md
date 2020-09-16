---
title: Serviços de teste com ferramentas de gRPC
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594402"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="07ea7-105">Serviços de teste com ferramentas de gRPC</span><span class="sxs-lookup"><span data-stu-id="07ea7-105">Test services with gRPC tools</span></span>

<span data-ttu-id="07ea7-106">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="07ea7-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="07ea7-107">As ferramentas estão disponíveis para gRPC que permitem aos desenvolvedores testar serviços sem criar aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="07ea7-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="07ea7-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) é uma ferramenta de linha de comando que fornece interação com os serviços do gRPC.</span><span class="sxs-lookup"><span data-stu-id="07ea7-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="07ea7-109">o [gRPCui](https://github.com/fullstorydev/grpcui) adiciona uma interface do usuário da Web interativa para gRPC.</span><span class="sxs-lookup"><span data-stu-id="07ea7-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="07ea7-110">Este artigo discute como:</span><span class="sxs-lookup"><span data-stu-id="07ea7-110">This article discusses how to:</span></span>

* <span data-ttu-id="07ea7-111">Baixe e instale o gRPCurl e o gRPCui.</span><span class="sxs-lookup"><span data-stu-id="07ea7-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="07ea7-112">Configure a reflexão do gRPC com um aplicativo gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07ea7-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="07ea7-113">Descubra e teste os serviços gRPC com o `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="07ea7-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="07ea7-114">Interaja com os serviços gRPCs por meio de um navegador usando o `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="07ea7-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="07ea7-115">Sobre o gRPCurl</span><span class="sxs-lookup"><span data-stu-id="07ea7-115">About gRPCurl</span></span>

<span data-ttu-id="07ea7-116">gRPCurl é uma ferramenta de linha de comando criada pela Comunidade do gRPC.</span><span class="sxs-lookup"><span data-stu-id="07ea7-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="07ea7-117">Seus recursos incluem:</span><span class="sxs-lookup"><span data-stu-id="07ea7-117">Its features include:</span></span>

* <span data-ttu-id="07ea7-118">Chamar serviços gRPCs, incluindo serviços de streaming.</span><span class="sxs-lookup"><span data-stu-id="07ea7-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="07ea7-119">Descoberta de serviço usando a [reflexão gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="07ea7-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="07ea7-120">Listar e descrever os serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="07ea7-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="07ea7-121">Funciona com servidores seguros (TLS) e não seguros (texto sem formatação).</span><span class="sxs-lookup"><span data-stu-id="07ea7-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="07ea7-122">Para obter informações sobre como baixar e instalar `grpcurl` o, consulte a [Home Page do GitHub do gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="07ea7-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="07ea7-123">Configurar a reflexão do gRPC</span><span class="sxs-lookup"><span data-stu-id="07ea7-123">Setup gRPC reflection</span></span>

<span data-ttu-id="07ea7-124">`grpcurl` precisa conhecer o contrato de serviços Protobuf antes de poder chamá-los.</span><span class="sxs-lookup"><span data-stu-id="07ea7-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="07ea7-125">Há duas maneiras de fazer isso:</span><span class="sxs-lookup"><span data-stu-id="07ea7-125">There are two ways to do this:</span></span>

* <span data-ttu-id="07ea7-126">Use a reflexão do gRPC para descobrir contratos de serviço.</span><span class="sxs-lookup"><span data-stu-id="07ea7-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="07ea7-127">Especifique arquivos *. proto* em argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="07ea7-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="07ea7-128">É mais fácil usar o gRPCurl com a reflexão do gRPC e a descoberta de serviços.</span><span class="sxs-lookup"><span data-stu-id="07ea7-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="07ea7-129">gRPC ASP.NET Core tem suporte interno para reflexão gRPC com o pacote [gRPC. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) .</span><span class="sxs-lookup"><span data-stu-id="07ea7-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="07ea7-130">Para configurar a reflexão em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="07ea7-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="07ea7-131">Adicionar `Grpc.AspNetCore.Server.Reflection` referência de pacote.</span><span class="sxs-lookup"><span data-stu-id="07ea7-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="07ea7-132">Registrar reflexão no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="07ea7-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="07ea7-133">`AddGrpcReflection` para registrar serviços que habilitam a reflexão.</span><span class="sxs-lookup"><span data-stu-id="07ea7-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="07ea7-134">`MapGrpcReflectionService` para adicionar um ponto de extremidade de serviço de reflexão.</span><span class="sxs-lookup"><span data-stu-id="07ea7-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="07ea7-135">Use `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="07ea7-135">Use `grpcurl`</span></span>

<span data-ttu-id="07ea7-136">O `-help` argumento explica `grpcurl` as opções de linha de comando:</span><span class="sxs-lookup"><span data-stu-id="07ea7-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="07ea7-137">Descobrir serviços</span><span class="sxs-lookup"><span data-stu-id="07ea7-137">Discover services</span></span>

<span data-ttu-id="07ea7-138">Use o `describe` verbo para exibir os serviços definidos pelo servidor:</span><span class="sxs-lookup"><span data-stu-id="07ea7-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
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

<span data-ttu-id="07ea7-139">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="07ea7-139">The preceding example:</span></span>

* <span data-ttu-id="07ea7-140">Executa o `describe` verbo no servidor `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="07ea7-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="07ea7-141">Imprime os serviços e os métodos retornados pela reflexão gRPC.</span><span class="sxs-lookup"><span data-stu-id="07ea7-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="07ea7-142">`Greeter` é um serviço implementado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="07ea7-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="07ea7-143">`ServerReflection` o serviço é adicionado pelo `Grpc.AspNetCore.Server.Reflection` pacote.</span><span class="sxs-lookup"><span data-stu-id="07ea7-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="07ea7-144">Combine `describe` com um serviço, método ou nome de mensagem para exibir seus detalhes:</span><span class="sxs-lookup"><span data-stu-id="07ea7-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="07ea7-145">Chamar serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="07ea7-145">Call gRPC services</span></span>

<span data-ttu-id="07ea7-146">Chame um serviço gRPC especificando um nome de serviço e método, junto com um argumento JSON que representa a mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="07ea7-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="07ea7-147">O JSON é convertido em Protobuf e enviado para o serviço.</span><span class="sxs-lookup"><span data-stu-id="07ea7-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="07ea7-148">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="07ea7-148">The preceding example:</span></span>

* <span data-ttu-id="07ea7-149">`-d` argumento especifica uma mensagem de solicitação com JSON.</span><span class="sxs-lookup"><span data-stu-id="07ea7-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="07ea7-150">Esse argumento deve vir antes do endereço do servidor e do nome do método.</span><span class="sxs-lookup"><span data-stu-id="07ea7-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="07ea7-151">Chama o `SayHello` método no `greeter.Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="07ea7-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="07ea7-152">Imprime a mensagem de resposta como JSON.</span><span class="sxs-lookup"><span data-stu-id="07ea7-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="07ea7-153">Sobre o gRPCui</span><span class="sxs-lookup"><span data-stu-id="07ea7-153">About gRPCui</span></span>

<span data-ttu-id="07ea7-154">gRPCui é uma interface do usuário da Web interativa para gRPC.</span><span class="sxs-lookup"><span data-stu-id="07ea7-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="07ea7-155">Ele se baseia no gRPCurl e oferece uma GUI para descobrir e testar os serviços do gRPC, semelhante às ferramentas HTTP como o postmaster.</span><span class="sxs-lookup"><span data-stu-id="07ea7-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="07ea7-156">Para obter informações sobre como baixar e instalar `grpcui` o, consulte a [Home Page do GitHub do gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="07ea7-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="07ea7-157">Usando `grpcui`</span><span class="sxs-lookup"><span data-stu-id="07ea7-157">Using `grpcui`</span></span>

<span data-ttu-id="07ea7-158">Execute `grpcui` com o endereço do servidor com o qual interagir como um argumento.</span><span class="sxs-lookup"><span data-stu-id="07ea7-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="07ea7-159">A ferramenta iniciará uma janela do navegador com a interface do usuário da Web interativa.</span><span class="sxs-lookup"><span data-stu-id="07ea7-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="07ea7-160">os serviços gRPCs são automaticamente descobertos usando a reflexão do gRPC.</span><span class="sxs-lookup"><span data-stu-id="07ea7-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![interface do usuário da Web do amgRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="07ea7-162">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="07ea7-162">Additional resources</span></span>

* [<span data-ttu-id="07ea7-163">Home Page do GitHub gRPCurl</span><span class="sxs-lookup"><span data-stu-id="07ea7-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="07ea7-164">Home Page do GitHub gRPCui</span><span class="sxs-lookup"><span data-stu-id="07ea7-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="07ea7-165">Grpc. AspNetCore. Server. Reflection</span><span class="sxs-lookup"><span data-stu-id="07ea7-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
