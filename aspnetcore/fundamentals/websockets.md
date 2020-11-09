---
title: Suporte ao WebSockets no ASP.NET Core
author: rick-anderson
description: Saiba como começar a usar o WebSockets no ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/websockets
ms.openlocfilehash: 83a41d503b2d56bca3f1bac14eeb9d54a8257642
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057772"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="11aa7-103">Suporte ao WebSockets no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11aa7-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="11aa7-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="11aa7-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="11aa7-105">Este artigo explica como começar a usar o WebSockets no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11aa7-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="11aa7-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) é um protocolo que permite canais de comunicação persistentes bidirecionais em conexões TCP.</span><span class="sxs-lookup"><span data-stu-id="11aa7-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="11aa7-107">Ele é usado em aplicativos que se beneficiam de comunicação rápida e em tempo real, como chat, painel e aplicativos de jogos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="11aa7-108">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="11aa7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="11aa7-109">[Como executar](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="11aa7-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="11aa7-110">[ASP.NET Core SignalR ](xref:signalr/introduction) é uma biblioteca que simplifica a adição de funcionalidades da Web em tempo real a aplicativos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="11aa7-111">Ele usa WebSockets sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="11aa7-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="11aa7-112">Para a maioria dos aplicativos, recomendamos SignalR sobre WebSockets brutos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="11aa7-113">SignalR fornece fallback de transporte para ambientes em que o WebSockets não está disponível.</span><span class="sxs-lookup"><span data-stu-id="11aa7-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="11aa7-114">Ele também fornece um modelo básico de aplicativo de chamada de procedimento remoto.</span><span class="sxs-lookup"><span data-stu-id="11aa7-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="11aa7-115">E na maioria dos cenários, SignalR não há uma desvantagem significativa no desempenho em comparação com o uso de WebSockets brutos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="11aa7-116">Para alguns aplicativos, o [gRPC no .net](xref:grpc/index) fornece uma alternativa para WebSockets.</span><span class="sxs-lookup"><span data-stu-id="11aa7-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="11aa7-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="11aa7-117">Prerequisites</span></span>

* <span data-ttu-id="11aa7-118">Qualquer sistema operacional compatível com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="11aa7-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="11aa7-119">Windows 7/Windows Server 2008 ou posterior</span><span class="sxs-lookup"><span data-stu-id="11aa7-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="11aa7-120">Linux</span><span class="sxs-lookup"><span data-stu-id="11aa7-120">Linux</span></span>
  * <span data-ttu-id="11aa7-121">macOS</span><span class="sxs-lookup"><span data-stu-id="11aa7-121">macOS</span></span>  
* <span data-ttu-id="11aa7-122">Se o aplicativo é executado no Windows com o IIS:</span><span class="sxs-lookup"><span data-stu-id="11aa7-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="11aa7-123">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="11aa7-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="11aa7-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="11aa7-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="11aa7-125">Os WebSockets devem ser habilitados.</span><span class="sxs-lookup"><span data-stu-id="11aa7-125">WebSockets must be enabled.</span></span> <span data-ttu-id="11aa7-126">Consulte a seção [suporte do IIS/IIS Express](#iisiis-express-support) .</span><span class="sxs-lookup"><span data-stu-id="11aa7-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="11aa7-127">Se o aplicativo é executado no [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="11aa7-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="11aa7-128">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="11aa7-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="11aa7-129">Para saber quais são os navegadores compatíveis, confira https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="11aa7-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="11aa7-130">Configurar o middleware</span><span class="sxs-lookup"><span data-stu-id="11aa7-130">Configure the middleware</span></span>

<span data-ttu-id="11aa7-131">Adicione o middleware do WebSockets no método `Configure` da classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="11aa7-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="11aa7-132">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="11aa7-132">The following settings can be configured:</span></span>

* <span data-ttu-id="11aa7-133">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="11aa7-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="11aa7-134">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-134">The default is two minutes.</span></span>
* <span data-ttu-id="11aa7-135">`ReceiveBufferSize` – o tamanho do buffer usado para receber dados.</span><span class="sxs-lookup"><span data-stu-id="11aa7-135">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="11aa7-136">Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados.</span><span class="sxs-lookup"><span data-stu-id="11aa7-136">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="11aa7-137">O padrão é 4 KB.</span><span class="sxs-lookup"><span data-stu-id="11aa7-137">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="11aa7-138">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="11aa7-138">The following settings can be configured:</span></span>

* <span data-ttu-id="11aa7-139">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="11aa7-139">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="11aa7-140">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-140">The default is two minutes.</span></span>
* <span data-ttu-id="11aa7-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> – o tamanho do buffer usado para receber dados.</span><span class="sxs-lookup"><span data-stu-id="11aa7-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="11aa7-142">Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados.</span><span class="sxs-lookup"><span data-stu-id="11aa7-142">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="11aa7-143">O padrão é 4 KB.</span><span class="sxs-lookup"><span data-stu-id="11aa7-143">The default is 4 KB.</span></span>
* <span data-ttu-id="11aa7-144">`AllowedOrigins` – Uma lista de valores de cabeçalho de origem permitidos para solicitações do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="11aa7-144">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="11aa7-145">Por padrão, todas as origens são permitidas.</span><span class="sxs-lookup"><span data-stu-id="11aa7-145">By default, all origins are allowed.</span></span> <span data-ttu-id="11aa7-146">Consulte "Restrição de origem do WebSocket" abaixo para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="11aa7-146">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="11aa7-147">Aceitar solicitações do WebSocket</span><span class="sxs-lookup"><span data-stu-id="11aa7-147">Accept WebSocket requests</span></span>

<span data-ttu-id="11aa7-148">Futuramente no ciclo de vida da solicitação (mais tarde no método `Configure` ou em um método de ação, por exemplo), verifique se é uma solicitação do WebSocket e aceite-a.</span><span class="sxs-lookup"><span data-stu-id="11aa7-148">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="11aa7-149">O exemplo a seguir é de uma fase posterior do método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="11aa7-149">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="11aa7-150">Uma solicitação do WebSocket pode entrar em qualquer URL, mas esse código de exemplo aceita apenas solicitações de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="11aa7-150">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="11aa7-151">Ao usar um WebSocket, você **deve** manter o pipeline de middleware em execução durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="11aa7-151">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="11aa7-152">Se tentar enviar ou receber uma mensagem do WebSocket após o término do pipeline de middleware, você poderá receber uma exceção semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="11aa7-152">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="11aa7-153">Se você estiver usando um serviço em segundo plano para gravar dados em um WebSocket, mantenha o pipeline do middleware em execução.</span><span class="sxs-lookup"><span data-stu-id="11aa7-153">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="11aa7-154">Faça isso usando um <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="11aa7-154">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="11aa7-155">Passe o `TaskCompletionSource` ao seu serviço em segundo plano e faça-o chamar <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> quando terminar com o WebSocket.</span><span class="sxs-lookup"><span data-stu-id="11aa7-155">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="11aa7-156">Em seguida `await` a propriedade <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante a solicitação, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="11aa7-156">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="11aa7-157">A exceção de WebSocket Closed também pode ocorrer ao retornar muito cedo de um método de ação.</span><span class="sxs-lookup"><span data-stu-id="11aa7-157">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="11aa7-158">Ao aceitar um soquete em um método de ação, aguarde o código que usa o soquete para ser concluído antes de retornar do método de ação.</span><span class="sxs-lookup"><span data-stu-id="11aa7-158">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="11aa7-159">Nunca use `Task.Wait`, `Task.Result` ou chamadas de bloqueio semelhantes para aguardar a conclusão do soquete, pois isso pode causar sérios problemas de threading.</span><span class="sxs-lookup"><span data-stu-id="11aa7-159">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="11aa7-160">Sempre use `await`.</span><span class="sxs-lookup"><span data-stu-id="11aa7-160">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="11aa7-161">Enviar e receber mensagens</span><span class="sxs-lookup"><span data-stu-id="11aa7-161">Send and receive messages</span></span>

<span data-ttu-id="11aa7-162">O método `AcceptWebSocketAsync` atualiza a conexão TCP para uma conexão WebSocket e fornece um objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="11aa7-162">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="11aa7-163">Use o objeto `WebSocket` para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="11aa7-163">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="11aa7-164">O código mostrado anteriormente que aceita a solicitação do WebSocket passa o objeto `WebSocket` para um método `Echo`.</span><span class="sxs-lookup"><span data-stu-id="11aa7-164">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="11aa7-165">O código recebe uma mensagem e envia de volta imediatamente a mesma mensagem.</span><span class="sxs-lookup"><span data-stu-id="11aa7-165">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="11aa7-166">As mensagens são enviadas e recebidas em um loop até que o cliente feche a conexão:</span><span class="sxs-lookup"><span data-stu-id="11aa7-166">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="11aa7-167">Ao aceitar a conexão WebSocket antes de iniciar o loop, o pipeline de middleware é encerrado.</span><span class="sxs-lookup"><span data-stu-id="11aa7-167">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="11aa7-168">Ao fechar o soquete, o pipeline é desenrolado.</span><span class="sxs-lookup"><span data-stu-id="11aa7-168">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="11aa7-169">Ou seja, a solicitação deixa de avançar no pipeline quando o WebSocket é aceito.</span><span class="sxs-lookup"><span data-stu-id="11aa7-169">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="11aa7-170">Quando o loop é concluído e o soquete é fechado, a solicitação continua a avançar no pipeline.</span><span class="sxs-lookup"><span data-stu-id="11aa7-170">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="11aa7-171">Tratar desconexões de cliente</span><span class="sxs-lookup"><span data-stu-id="11aa7-171">Handle client disconnects</span></span>

<span data-ttu-id="11aa7-172">O servidor não é informado automaticamente quando o cliente se desconecta devido à perda de conectividade.</span><span class="sxs-lookup"><span data-stu-id="11aa7-172">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="11aa7-173">O servidor recebe uma mensagem de desconexão apenas quando o cliente a envia, e isso é possível somente quando há conexão com a Internet.</span><span class="sxs-lookup"><span data-stu-id="11aa7-173">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="11aa7-174">Se quiser tomar alguma medida quando isso ocorrer, defina um tempo limite, caso não receba nada do cliente após uma determinada janela de tempo.</span><span class="sxs-lookup"><span data-stu-id="11aa7-174">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="11aa7-175">Se o cliente não envia mensagens com frequência, e você prefere não definir um tempo limite apenas porque a conexão fica ociosa, peça ao cliente para usar um temporizador a fim de enviar uma mensagem de ping a cada X segundos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-175">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="11aa7-176">No servidor, se uma mensagem não chegar dentro de 2\*X segundos após a mensagem anterior, encerre a conexão e informe que o cliente se desconectou.</span><span class="sxs-lookup"><span data-stu-id="11aa7-176">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="11aa7-177">Aguarde o dobro do intervalo de tempo esperado a fim de deixar um tempo extra para atrasos na rede, que possam atrasar a mensagem de ping.</span><span class="sxs-lookup"><span data-stu-id="11aa7-177">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="11aa7-178">Restrição de origem do WebSocket</span><span class="sxs-lookup"><span data-stu-id="11aa7-178">WebSocket origin restriction</span></span>

<span data-ttu-id="11aa7-179">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="11aa7-179">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="11aa7-180">Navegadores **não** :</span><span class="sxs-lookup"><span data-stu-id="11aa7-180">Browsers do **not** :</span></span>

* <span data-ttu-id="11aa7-181">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="11aa7-181">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="11aa7-182">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="11aa7-182">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="11aa7-183">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="11aa7-183">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="11aa7-184">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="11aa7-184">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="11aa7-185">Se você estiver hospedando o servidor em "https://server.com" e hospedando seu cliente em "https://client.com", adicione "https://client.com" à lista `AllowedOrigins` para o WebSockets verificar.</span><span class="sxs-lookup"><span data-stu-id="11aa7-185">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="11aa7-186">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="11aa7-186">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="11aa7-187">**Não** use esses cabeçalhos como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="11aa7-187">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="11aa7-188">Suporte ao IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="11aa7-188">IIS/IIS Express support</span></span>

<span data-ttu-id="11aa7-189">O Windows Server 2012 ou posterior e o Windows 8 ou posterior com o IIS/IIS Express 8 ou posterior são compatíveis com o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="11aa7-189">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="11aa7-190">WebSockets estão sempre habilitados ao usar o IIS Express.</span><span class="sxs-lookup"><span data-stu-id="11aa7-190">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="11aa7-191">Habilitar WebSockets no IIS</span><span class="sxs-lookup"><span data-stu-id="11aa7-191">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="11aa7-192">Para habilitar o suporte para o protocolo WebSocket no Windows Server 2012 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="11aa7-192">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="11aa7-193">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="11aa7-193">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="11aa7-194">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-194">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span>
1. <span data-ttu-id="11aa7-195">Selecione **Instalação baseada em função ou em recurso** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-195">Select **Role-based or Feature-based Installation** .</span></span> <span data-ttu-id="11aa7-196">Selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-196">Select **Next** .</span></span>
1. <span data-ttu-id="11aa7-197">Selecione o servidor apropriado (o servidor local é selecionado por padrão).</span><span class="sxs-lookup"><span data-stu-id="11aa7-197">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="11aa7-198">Selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-198">Select **Next** .</span></span>
1. <span data-ttu-id="11aa7-199">Expanda **Servidor Web (IIS)** na árvore **Funções** , expanda **Servidor Web** e, em seguida, expanda **Desenvolvimento de Aplicativos** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-199">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server** , and then expand **Application Development** .</span></span>
1. <span data-ttu-id="11aa7-200">Selecione o **Protocolo WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-200">Select **WebSocket Protocol** .</span></span> <span data-ttu-id="11aa7-201">Selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-201">Select **Next** .</span></span>
1. <span data-ttu-id="11aa7-202">Se não forem necessários recursos adicionais, selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-202">If additional features aren't needed, select **Next** .</span></span>
1. <span data-ttu-id="11aa7-203">Selecione **Instalar** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-203">Select **Install** .</span></span>
1. <span data-ttu-id="11aa7-204">Quando a instalação for concluída, selecione **Fechar** para sair do assistente.</span><span class="sxs-lookup"><span data-stu-id="11aa7-204">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="11aa7-205">Para habilitar o suporte para o protocolo WebSocket no Windows 8 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="11aa7-205">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="11aa7-206">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="11aa7-206">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="11aa7-207">Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="11aa7-207">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="11aa7-208">Abra os seguintes nós: **serviços de informações da Internet** os  >  recursos de desenvolvimento de aplicativos **dos serviços World Wide Webs**  >  **Application Development Features** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-208">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features** .</span></span>
1. <span data-ttu-id="11aa7-209">Selecione o recurso **Protocolo WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-209">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="11aa7-210">Selecione **OK** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-210">Select **OK** .</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="11aa7-211">Desabilite o WebSocket ao usar o socket.io no Node.js</span><span class="sxs-lookup"><span data-stu-id="11aa7-211">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="11aa7-212">Se estiver usando o suporte WebSocket no [Socket.Io](https://socket.io/) no [Node.js](https://nodejs.org/), desabilite o módulo WebSocket padrão do IIS usando o `webSocket` elemento em *web.config* ou *applicationHost.config* . Se essa etapa não for executada, o módulo WebSocket do IIS tentará lidar com a comunicação WebSocket em vez de Node.js e o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11aa7-212">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config* . If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="11aa7-213">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="11aa7-213">Sample app</span></span>

<span data-ttu-id="11aa7-214">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompanha este artigo é um aplicativo de eco.</span><span class="sxs-lookup"><span data-stu-id="11aa7-214">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="11aa7-215">Ele tem uma página da Web que faz conexões WebSocket e o servidor reenvia para o cliente todas as mensagens recebidas.</span><span class="sxs-lookup"><span data-stu-id="11aa7-215">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="11aa7-216">Execute o aplicativo em um prompt de comando (ele não está configurado para execução no Visual Studio com o IIS Express) e navegue para http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="11aa7-216">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="11aa7-217">A página da Web exibe o status de conexão no canto superior esquerdo:</span><span class="sxs-lookup"><span data-stu-id="11aa7-217">The web page shows the connection status in the upper left:</span></span>

![Estado inicial da página da Web](websockets/_static/start.png)

<span data-ttu-id="11aa7-219">Selecione **Conectar** para enviar uma solicitação WebSocket para a URL exibida.</span><span class="sxs-lookup"><span data-stu-id="11aa7-219">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="11aa7-220">Insira uma mensagem de teste e selecione **Enviar** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-220">Enter a test message and select **Send** .</span></span> <span data-ttu-id="11aa7-221">Quando terminar, selecione **Fechar Soquete** .</span><span class="sxs-lookup"><span data-stu-id="11aa7-221">When done, select **Close Socket** .</span></span> <span data-ttu-id="11aa7-222">A seção **Log de Comunicação** relata cada ação de abertura, envio e fechamento conforme ela ocorre.</span><span class="sxs-lookup"><span data-stu-id="11aa7-222">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Estado inicial da página da Web](websockets/_static/end.png)

