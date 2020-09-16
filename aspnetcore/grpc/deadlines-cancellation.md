---
title: Serviços gRPCs confiáveis com prazos e cancelamento
author: jamesnk
description: Saiba como criar serviços gRPC confiáveis com prazos e cancelamento no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: 59b737a032ea37a554ad5ddd0f4d44e4e1602d88
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594405"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="d241f-103">Serviços gRPCs confiáveis com prazos e cancelamento</span><span class="sxs-lookup"><span data-stu-id="d241f-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="d241f-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d241f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d241f-105">Prazos e cancelamento são recursos usados por clientes gRPC para anular chamadas em andamento.</span><span class="sxs-lookup"><span data-stu-id="d241f-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="d241f-106">Este artigo discute por que prazos e cancelamento são importantes e como usá-los em aplicativos .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="d241f-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="d241f-107">Prazos</span><span class="sxs-lookup"><span data-stu-id="d241f-107">Deadlines</span></span>

<span data-ttu-id="d241f-108">Um prazo final permite que um cliente gRPC especifique quanto tempo ele aguardará até que uma chamada seja concluída.</span><span class="sxs-lookup"><span data-stu-id="d241f-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="d241f-109">Quando um prazo é excedido, a chamada é cancelada.</span><span class="sxs-lookup"><span data-stu-id="d241f-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="d241f-110">Definir um prazo é importante porque ele fornece um limite superior de quanto tempo uma chamada pode ser executada para.</span><span class="sxs-lookup"><span data-stu-id="d241f-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="d241f-111">Ele impede que os serviços de comportamento inadequado executem a execução contínua e esgotando os recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="d241f-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="d241f-112">Os prazos são uma ferramenta útil para a criação de aplicativos confiáveis e devem ser configurados.</span><span class="sxs-lookup"><span data-stu-id="d241f-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="d241f-113">Configuração do prazo:</span><span class="sxs-lookup"><span data-stu-id="d241f-113">Deadline configuration:</span></span>

* <span data-ttu-id="d241f-114">Um prazo é configurado usando `CallOptions.Deadline` quando uma chamada é feita.</span><span class="sxs-lookup"><span data-stu-id="d241f-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="d241f-115">Não há nenhum valor de prazo padrão.</span><span class="sxs-lookup"><span data-stu-id="d241f-115">There is no default deadline value.</span></span> <span data-ttu-id="d241f-116">chamadas gRPC não são limitadas por tempo, a menos que um prazo seja especificado.</span><span class="sxs-lookup"><span data-stu-id="d241f-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="d241f-117">Um prazo final é a hora UTC de quando o prazo é excedido.</span><span class="sxs-lookup"><span data-stu-id="d241f-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="d241f-118">Por exemplo, `DateTime.UtcNow.AddSeconds(5)` é um prazo de 5 segundos a partir de agora.</span><span class="sxs-lookup"><span data-stu-id="d241f-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="d241f-119">Se uma hora passada ou atual for usada, a chamada excederá imediatamente o prazo.</span><span class="sxs-lookup"><span data-stu-id="d241f-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="d241f-120">O prazo é enviado com a chamada gRPC para o serviço e é acompanhado de forma independente pelo cliente e pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="d241f-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="d241f-121">É possível que uma chamada gRPC seja concluída em uma máquina, mas no momento em que a resposta foi retornada ao cliente, o prazo foi excedido.</span><span class="sxs-lookup"><span data-stu-id="d241f-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="d241f-122">Se um prazo for excedido, o cliente e o serviço terão comportamento diferente:</span><span class="sxs-lookup"><span data-stu-id="d241f-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="d241f-123">O cliente anula imediatamente a solicitação HTTP subjacente e gera um `DeadlineExceeded` erro.</span><span class="sxs-lookup"><span data-stu-id="d241f-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="d241f-124">O aplicativo cliente pode optar por capturar o erro e exibir uma mensagem de tempo limite para o usuário.</span><span class="sxs-lookup"><span data-stu-id="d241f-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="d241f-125">No servidor, a solicitação HTTP em execução é anulada e [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) é gerado.</span><span class="sxs-lookup"><span data-stu-id="d241f-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="d241f-126">Embora a solicitação HTTP seja anulada, a chamada gRPC continua a ser executada no servidor até que o método seja concluído.</span><span class="sxs-lookup"><span data-stu-id="d241f-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="d241f-127">É importante que o token de cancelamento seja passado para métodos assíncronos para que eles sejam cancelados junto com a chamada.</span><span class="sxs-lookup"><span data-stu-id="d241f-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="d241f-128">Por exemplo, passar um token de cancelamento para consultas de banco de dados assíncrono e solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d241f-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="d241f-129">Passar um token de cancelamento permite que a chamada cancelada seja concluída rapidamente no servidor e libera recursos para outras chamadas.</span><span class="sxs-lookup"><span data-stu-id="d241f-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="d241f-130">Configure `CallOptions.Deadline` para definir um prazo para uma chamada gRPC:</span><span class="sxs-lookup"><span data-stu-id="d241f-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="d241f-131">Usando `ServerCallContext.CancellationToken` o em um serviço gRPC:</span><span class="sxs-lookup"><span data-stu-id="d241f-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="d241f-132">Propagando prazos</span><span class="sxs-lookup"><span data-stu-id="d241f-132">Propagating deadlines</span></span>

<span data-ttu-id="d241f-133">Quando uma chamada gRPC é feita de um serviço gRPC em execução, o prazo final deve ser propagado.</span><span class="sxs-lookup"><span data-stu-id="d241f-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="d241f-134">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d241f-134">For example:</span></span>

1. <span data-ttu-id="d241f-135">O aplicativo cliente chama `FrontendService.GetUser` um prazo final.</span><span class="sxs-lookup"><span data-stu-id="d241f-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="d241f-136">`FrontendService` chama `UserService.GetUser`.</span><span class="sxs-lookup"><span data-stu-id="d241f-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="d241f-137">O prazo especificado pelo cliente deve ser especificado com a nova chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="d241f-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="d241f-138">`UserService.GetUser` recebe o prazo final.</span><span class="sxs-lookup"><span data-stu-id="d241f-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="d241f-139">O tempo limite foi atingido corretamente se o prazo do aplicativo cliente for excedido.</span><span class="sxs-lookup"><span data-stu-id="d241f-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="d241f-140">O contexto de chamada fornece a data limite com `ServerCallContext.Deadline` :</span><span class="sxs-lookup"><span data-stu-id="d241f-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="d241f-141">Propagar manualmente os prazos pode ser trabalhoso.</span><span class="sxs-lookup"><span data-stu-id="d241f-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="d241f-142">O prazo final precisa ser passado para cada chamada e é fácil de perder acidentalmente.</span><span class="sxs-lookup"><span data-stu-id="d241f-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="d241f-143">Uma solução automática está disponível com o gRPC Client Factory.</span><span class="sxs-lookup"><span data-stu-id="d241f-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="d241f-144">Especificando `EnableCallContextPropagation` :</span><span class="sxs-lookup"><span data-stu-id="d241f-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="d241f-145">O propaga automaticamente o data limite e o token de cancelamento para chamadas filhas.</span><span class="sxs-lookup"><span data-stu-id="d241f-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="d241f-146">É uma excelente maneira de garantir que cenários de gRPC complexos e aninhados sempre propaguem o prazo e o cancelamento.</span><span class="sxs-lookup"><span data-stu-id="d241f-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="d241f-147">Para obter mais informações, consulte <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="d241f-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="d241f-148">Cancelamento</span><span class="sxs-lookup"><span data-stu-id="d241f-148">Cancellation</span></span>

<span data-ttu-id="d241f-149">O cancelamento permite que um cliente gRPC cancele chamadas de longa execução que não são mais necessárias.</span><span class="sxs-lookup"><span data-stu-id="d241f-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="d241f-150">Por exemplo, uma chamada gRPC que transmite atualizações em tempo real é iniciada quando o usuário visita uma página em um site.</span><span class="sxs-lookup"><span data-stu-id="d241f-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="d241f-151">O fluxo deve ser cancelado quando o usuário navega para fora da página.</span><span class="sxs-lookup"><span data-stu-id="d241f-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="d241f-152">Uma chamada gRPC pode ser cancelada no cliente passando um token de cancelamento com [calloptions. CancellationToken](xref:System.Threading.CancellationToken) ou chamando `Dispose` na chamada.</span><span class="sxs-lookup"><span data-stu-id="d241f-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="d241f-153">os serviços gRPCs que podem ser cancelados devem:</span><span class="sxs-lookup"><span data-stu-id="d241f-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="d241f-154">Passe `ServerCallContext.CancellationToken` para métodos assíncronos.</span><span class="sxs-lookup"><span data-stu-id="d241f-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="d241f-155">O cancelamento de métodos assíncronos permite que a chamada no servidor seja concluída rapidamente.</span><span class="sxs-lookup"><span data-stu-id="d241f-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="d241f-156">Propague o token de cancelamento para chamadas filhas.</span><span class="sxs-lookup"><span data-stu-id="d241f-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="d241f-157">Propagar o token de cancelamento garante que as chamadas filhas sejam canceladas com seu pai.</span><span class="sxs-lookup"><span data-stu-id="d241f-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="d241f-158">o [gRPC Client Factory](xref:grpc/clientfactory) e `EnableCallContextPropagation()` propaga automaticamente o token de cancelamento.</span><span class="sxs-lookup"><span data-stu-id="d241f-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d241f-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d241f-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
