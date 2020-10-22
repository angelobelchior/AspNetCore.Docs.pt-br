---
title: SignalRSolução de problemas ASP.NET Core conexão
author: bradygaster
description: ASP.NET Core SignalR solução de problemas de conexão.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: 2ffe8bcf4bef5dcb9fc74513c7507e5cb3a6b7cb
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379572"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="9f982-103">Solucionar erros de conexão</span><span class="sxs-lookup"><span data-stu-id="9f982-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="9f982-104">Esta seção fornece ajuda com erros que podem ocorrer ao tentar estabelecer uma conexão com um hub de ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="9f982-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="9f982-105">Código de resposta 404</span><span class="sxs-lookup"><span data-stu-id="9f982-105">Response code 404</span></span>

<span data-ttu-id="9f982-106">Ao usar Websockets e `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="9f982-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="9f982-107">Ao usar vários servidores sem sessões adesivas, a conexão pode ser iniciada em um servidor e, em seguida, alternar para outro servidor.</span><span class="sxs-lookup"><span data-stu-id="9f982-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="9f982-108">O outro servidor não está ciente da conexão anterior.</span><span class="sxs-lookup"><span data-stu-id="9f982-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="9f982-109">Verifique se o cliente está se conectando ao ponto de extremidade correto.</span><span class="sxs-lookup"><span data-stu-id="9f982-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="9f982-110">Por exemplo, o servidor está hospedado em `http://127.0.0.1:5000/hub/myHub` e o cliente está tentando se conectar ao `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="9f982-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="9f982-111">Se a conexão usar a ID e levar muito tempo para enviar uma solicitação ao servidor após a negociação, o servidor:</span><span class="sxs-lookup"><span data-stu-id="9f982-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="9f982-112">Exclui a ID.</span><span class="sxs-lookup"><span data-stu-id="9f982-112">Deletes the ID.</span></span>
  * <span data-ttu-id="9f982-113">Retorna um 404.</span><span class="sxs-lookup"><span data-stu-id="9f982-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="9f982-114">Código de resposta 400 ou 503</span><span class="sxs-lookup"><span data-stu-id="9f982-114">Response code 400 or 503</span></span>

<span data-ttu-id="9f982-115">Para o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="9f982-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="9f982-116">Esse erro geralmente é causado por um cliente que usa apenas o transporte WebSockets, mas o protocolo WebSockets não está habilitado no servidor.</span><span class="sxs-lookup"><span data-stu-id="9f982-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="9f982-117">Código de resposta 307</span><span class="sxs-lookup"><span data-stu-id="9f982-117">Response code 307</span></span>

<span data-ttu-id="9f982-118">Ao usar Websockets e `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="9f982-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="9f982-119">Esse erro também pode ocorrer durante a solicitação Negotiate.</span><span class="sxs-lookup"><span data-stu-id="9f982-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="9f982-120">Causa comum:</span><span class="sxs-lookup"><span data-stu-id="9f982-120">Common cause:</span></span>
* <span data-ttu-id="9f982-121">O aplicativo é configurado para impor o HTTPS chamando `UseHttpsRedirection` em `Startup` ou impõe HTTPS por meio da regra de regravação de URL.</span><span class="sxs-lookup"><span data-stu-id="9f982-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="9f982-122">Possível solução:</span><span class="sxs-lookup"><span data-stu-id="9f982-122">Possible solution:</span></span>
* <span data-ttu-id="9f982-123">Altere a URL no lado do cliente de "http" para "https".</span><span class="sxs-lookup"><span data-stu-id="9f982-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="9f982-124">Código de resposta 405</span><span class="sxs-lookup"><span data-stu-id="9f982-124">Response code 405</span></span>

<span data-ttu-id="9f982-125">Código de status http 405-método não permitido</span><span class="sxs-lookup"><span data-stu-id="9f982-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="9f982-126">O aplicativo não tem [CORS](xref:signalr/security#cross-origin-resource-sharing) habilitado</span><span class="sxs-lookup"><span data-stu-id="9f982-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="9f982-127">Código de resposta 0</span><span class="sxs-lookup"><span data-stu-id="9f982-127">Response code 0</span></span>

<span data-ttu-id="9f982-128">Código de status http 0-geralmente um problema de [CORS](xref:signalr/security#cross-origin-resource-sharing) , nenhum código de status é fornecido</span><span class="sxs-lookup"><span data-stu-id="9f982-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="9f982-129">Adicionar as origens esperadas a `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="9f982-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="9f982-130">Adicione `.AllowCredentials()` à sua política de CORS.</span><span class="sxs-lookup"><span data-stu-id="9f982-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="9f982-131">Não é possível usar `.AllowAnyOrigin()` ou `.WithOrigins("*")` com esta opção</span><span class="sxs-lookup"><span data-stu-id="9f982-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="9f982-132">Código de resposta 413</span><span class="sxs-lookup"><span data-stu-id="9f982-132">Response code 413</span></span>

<span data-ttu-id="9f982-133">Código de status HTTP 413-o conteúdo é muito grande</span><span class="sxs-lookup"><span data-stu-id="9f982-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="9f982-134">Isso geralmente é causado por ter um token de acesso acima de 4K.</span><span class="sxs-lookup"><span data-stu-id="9f982-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="9f982-135">Se estiver usando o SignalR serviço do Azure, reduza o tamanho do token Personalizando as declarações sendo enviadas por meio do serviço com:</span><span class="sxs-lookup"><span data-stu-id="9f982-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="9f982-136">Falhas de rede transitórias</span><span class="sxs-lookup"><span data-stu-id="9f982-136">Transient network failures</span></span>

<span data-ttu-id="9f982-137">Falhas de rede transitórias podem fechar a SignalR conexão.</span><span class="sxs-lookup"><span data-stu-id="9f982-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="9f982-138">O servidor pode interpretar a conexão fechada como uma desconexão de cliente normal.</span><span class="sxs-lookup"><span data-stu-id="9f982-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="9f982-139">Para obter mais informações sobre por que um cliente desconectado nesses casos [coleta logs do cliente e do servidor](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9f982-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>