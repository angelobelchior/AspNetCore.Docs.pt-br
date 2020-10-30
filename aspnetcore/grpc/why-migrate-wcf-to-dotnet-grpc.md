---
title: Por que migrar o WCF para o ASP.NET Core gRPC
author: markrendle
description: Este artigo fornece um resumo de por que ASP.NET Core gRPC é uma boa opção para desenvolvedores de Windows Communication Foundation (WCF) que desejam migrar para arquiteturas e plataformas modernas.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058682"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="bae73-103">gRPC para desenvolvedores de Windows Communication Foundation (WCF)</span><span class="sxs-lookup"><span data-stu-id="bae73-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="bae73-104">Este artigo fornece um resumo de por que ASP.NET Core gRPC é uma boa opção para desenvolvedores de Windows Communication Foundation (WCF) que desejam migrar para arquiteturas e plataformas modernas.</span><span class="sxs-lookup"><span data-stu-id="bae73-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="bae73-105">Comparação com o WCF</span><span class="sxs-lookup"><span data-stu-id="bae73-105">Comparison to WCF</span></span>

<span data-ttu-id="bae73-106">Embora a implementação e a abordagem sejam diferentes para gRPC, a experiência de desenvolvimento e consumo de serviços com o gRPC deve ser intuitiva para desenvolvedores do WCF.</span><span class="sxs-lookup"><span data-stu-id="bae73-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="bae73-107">O WCF e o gRPC são estruturas RPC (chamada de procedimento remoto) com os mesmos objetivos:</span><span class="sxs-lookup"><span data-stu-id="bae73-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="bae73-108">Torne possível codificar como se o cliente e o servidor estivessem na mesma plataforma.</span><span class="sxs-lookup"><span data-stu-id="bae73-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="bae73-109">Forneça uma API de rede portátil simplificada.</span><span class="sxs-lookup"><span data-stu-id="bae73-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="bae73-110">Ambas as plataformas compartilham a necessidade de declarar e implementar uma interface, embora o processo para declarar a interface seja diferente.</span><span class="sxs-lookup"><span data-stu-id="bae73-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="bae73-111">Os muitos tipos de chamadas RPC que o gRPC dá suporte ao MAP bem para as associações disponíveis para os serviços WCF.</span><span class="sxs-lookup"><span data-stu-id="bae73-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="bae73-112">Para obter mais informações e exemplos, consulte [migrar uma solução WCF para gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="bae73-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="bae73-113">Benefícios do gRPC</span><span class="sxs-lookup"><span data-stu-id="bae73-113">Benefits of gRPC</span></span>

<span data-ttu-id="bae73-114">o gRPC fornece uma estrutura melhor do que outras abordagens pelos seguintes motivos.</span><span class="sxs-lookup"><span data-stu-id="bae73-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="bae73-115">Desempenho</span><span class="sxs-lookup"><span data-stu-id="bae73-115">Performance</span></span>

<span data-ttu-id="bae73-116">gRPC usa HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bae73-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="bae73-117">Em contraste com HTTP/1.1, HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="bae73-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="bae73-118">É um protocolo binário menor e mais rápido.</span><span class="sxs-lookup"><span data-stu-id="bae73-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="bae73-119">É mais eficiente para os computadores analisarem.</span><span class="sxs-lookup"><span data-stu-id="bae73-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="bae73-120">Dá suporte à multiplexação de solicitações em uma única conexão.</span><span class="sxs-lookup"><span data-stu-id="bae73-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="bae73-121">A multiplexação permite que várias solicitações sejam enviadas em uma conexão sem que as solicitações sejam bloqueadas.</span><span class="sxs-lookup"><span data-stu-id="bae73-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="bae73-122">No HTTP/1.1, o bloqueio é conhecido como "bloqueio de cabeçote de linha (Chol)".</span><span class="sxs-lookup"><span data-stu-id="bae73-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="bae73-123">gRPC usa Protobuf, um formato binário eficiente, para serializar mensagens.</span><span class="sxs-lookup"><span data-stu-id="bae73-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="bae73-124">As mensagens Protobuf são:</span><span class="sxs-lookup"><span data-stu-id="bae73-124">Protobuf messages are:</span></span>
* <span data-ttu-id="bae73-125">Rápido de serializar e desserializar.</span><span class="sxs-lookup"><span data-stu-id="bae73-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="bae73-126">Use menos largura de banda do que os formatos baseados em texto.</span><span class="sxs-lookup"><span data-stu-id="bae73-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="bae73-127">o gRPC é uma boa solução para dispositivos móveis e redes com restrições de largura de banda.</span><span class="sxs-lookup"><span data-stu-id="bae73-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="bae73-128">Interoperabilidade</span><span class="sxs-lookup"><span data-stu-id="bae73-128">Interoperability</span></span>

<span data-ttu-id="bae73-129">Há ferramentas e bibliotecas de gRPC para todas as principais linguagens de programação e plataformas, incluindo .NET, Java, Python, go, C++, Node.js, Swift, Dart, Ruby e PHP.</span><span class="sxs-lookup"><span data-stu-id="bae73-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="bae73-130">Graças ao formato de conexão binária Protobuf e à geração de código eficiente para cada plataforma, os desenvolvedores podem criar aplicativos de multiplataforma e com desempenho cruzado.</span><span class="sxs-lookup"><span data-stu-id="bae73-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="bae73-131">Usabilidade e produtividade</span><span class="sxs-lookup"><span data-stu-id="bae73-131">Usability and productivity</span></span>

<span data-ttu-id="bae73-132">gRPC é uma solução RPC abrangente.</span><span class="sxs-lookup"><span data-stu-id="bae73-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="bae73-133">Ele funciona consistentemente em vários idiomas e plataformas.</span><span class="sxs-lookup"><span data-stu-id="bae73-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="bae73-134">Ele também fornece ferramentas excelentes, com grande parte do código clichê gerado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bae73-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="bae73-135">Como o WCF, o gRPC gera automaticamente mensagens e um cliente com rigidez de tipos.</span><span class="sxs-lookup"><span data-stu-id="bae73-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="bae73-136">O tempo do desenvolvedor é liberado para se concentrar na lógica de negócios.</span><span class="sxs-lookup"><span data-stu-id="bae73-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="bae73-137">Streaming</span><span class="sxs-lookup"><span data-stu-id="bae73-137">Streaming</span></span>

<span data-ttu-id="bae73-138">o gRPC tem streaming bidirecional completo, que fornece funcionalidade semelhante aos serviços Full duplex do WCF.</span><span class="sxs-lookup"><span data-stu-id="bae73-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="bae73-139">o streaming gRPC pode operar em conexões de Internet regulares, balanceadores de carga e malhas de serviço.</span><span class="sxs-lookup"><span data-stu-id="bae73-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="bae73-140">Prazos, tempos limite e cancelamento</span><span class="sxs-lookup"><span data-stu-id="bae73-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="bae73-141">gRPC permite que os clientes especifiquem um tempo máximo para que um RPC seja concluído.</span><span class="sxs-lookup"><span data-stu-id="bae73-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="bae73-142">Se o prazo especificado for excedido, o servidor poderá cancelar a operação independentemente do cliente.</span><span class="sxs-lookup"><span data-stu-id="bae73-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="bae73-143">Os prazos finais e cancelamentos podem ser propagados por meio de chamadas gRPC subsequentes para ajudar a impor limites de uso de recursos.</span><span class="sxs-lookup"><span data-stu-id="bae73-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="bae73-144">Os clientes podem parar as operações quando um prazo é excedido ou antes, se necessário.</span><span class="sxs-lookup"><span data-stu-id="bae73-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="bae73-145">Por exemplo, os clientes podem parar as operações devido a uma interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="bae73-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="bae73-146">Segurança</span><span class="sxs-lookup"><span data-stu-id="bae73-146">Security</span></span>

<span data-ttu-id="bae73-147">gRPC pode usar TLS e HTTP/2 para fornecer uma conexão criptografada de ponta a ponta entre o cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="bae73-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="bae73-148">O suporte para autenticação de certificado de cliente aumenta ainda mais a segurança e a confiança entre o cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="bae73-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="bae73-149">gRPC como um caminho de migração para WCF para .NET Core e .NET 5</span><span class="sxs-lookup"><span data-stu-id="bae73-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="bae73-150">O .NET Core e o .NET 5 marcam uma mudança no modo como a Microsoft oferece soluções de comunicação remota para os desenvolvedores que desejam fornecer serviços em uma variedade de plataformas.</span><span class="sxs-lookup"><span data-stu-id="bae73-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="bae73-151">O .NET Core e o .NET 5 oferecem suporte à [chamada de serviços WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), mas não oferecem suporte ao lado do servidor para hospedar o WCF.</span><span class="sxs-lookup"><span data-stu-id="bae73-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="bae73-152">Há dois caminhos recomendados para modernizar aplicativos WCF:</span><span class="sxs-lookup"><span data-stu-id="bae73-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="bae73-153">o gRPC é criado em tecnologias modernas e surgiu como a escolha mais popular em toda a comunidade de desenvolvedores para aplicativos RPC.</span><span class="sxs-lookup"><span data-stu-id="bae73-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="bae73-154">A partir do .NET Core 3,0, as plataformas .NET modernas têm suporte excelente para gRPC.</span><span class="sxs-lookup"><span data-stu-id="bae73-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="bae73-155">Migrar serviços WCF para usar o gRPC ajuda a fornecer os recursos de RPC, o desempenho, uma interoperabilidade necessária em aplicativos modernos.</span><span class="sxs-lookup"><span data-stu-id="bae73-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="bae73-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) é um esforço da Comunidade para dar suporte à Hospedagem de serviços WCF para .NET Core e .NET 5.</span><span class="sxs-lookup"><span data-stu-id="bae73-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="bae73-157">Uma versão de visualização está disponível e o projeto está funcionando para ser pronto para produção.</span><span class="sxs-lookup"><span data-stu-id="bae73-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="bae73-158">O CoreWCF só dá suporte a um subconjunto dos recursos do WCF e .NET Framework aplicativos que migram para usá-lo precisarão de alterações de código e testes para serem bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="bae73-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="bae73-159">CoreWCF é uma boa opção se um aplicativo precisa manter a compatibilidade com os clientes existentes que chamam os serviços WCF.</span><span class="sxs-lookup"><span data-stu-id="bae73-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="bae73-160">Introdução</span><span class="sxs-lookup"><span data-stu-id="bae73-160">Get started</span></span>

<span data-ttu-id="bae73-161">Para obter orientações detalhadas sobre a criação de serviços gRPCs em ASP.NET Core para desenvolvedores do WCF, consulte [ASP.NET Core gRPC para desenvolvedores do WCF](/dotnet/architecture/grpc-for-wcf-developers)</span><span class="sxs-lookup"><span data-stu-id="bae73-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
