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
# <a name="troubleshoot-connection-errors"></a>Solucionar erros de conexão

Esta seção fornece ajuda com erros que podem ocorrer ao tentar estabelecer uma conexão com um hub de ASP.NET Core SignalR .

### <a name="response-code-404"></a>Código de resposta 404

Ao usar Websockets e `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* Ao usar vários servidores sem sessões adesivas, a conexão pode ser iniciada em um servidor e, em seguida, alternar para outro servidor. O outro servidor não está ciente da conexão anterior.
* Verifique se o cliente está se conectando ao ponto de extremidade correto. Por exemplo, o servidor está hospedado em `http://127.0.0.1:5000/hub/myHub` e o cliente está tentando se conectar ao `http://127.0.0.1:5000/myHub` .
* Se a conexão usar a ID e levar muito tempo para enviar uma solicitação ao servidor após a negociação, o servidor:

  * Exclui a ID.
  * Retorna um 404.

### <a name="response-code-400-or-503"></a>Código de resposta 400 ou 503

Para o seguinte erro:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Esse erro geralmente é causado por um cliente que usa apenas o transporte WebSockets, mas o protocolo WebSockets não está habilitado no servidor.

### <a name="response-code-307"></a>Código de resposta 307

Ao usar Websockets e `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Esse erro também pode ocorrer durante a solicitação Negotiate.

Causa comum:
* O aplicativo é configurado para impor o HTTPS chamando `UseHttpsRedirection` em `Startup` ou impõe HTTPS por meio da regra de regravação de URL.

Possível solução:
* Altere a URL no lado do cliente de "http" para "https". `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Código de resposta 405

Código de status http 405-método não permitido

* O aplicativo não tem [CORS](xref:signalr/security#cross-origin-resource-sharing) habilitado

### <a name="response-code-0"></a>Código de resposta 0

Código de status http 0-geralmente um problema de [CORS](xref:signalr/security#cross-origin-resource-sharing) , nenhum código de status é fornecido

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Adicionar as origens esperadas a `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Adicione `.AllowCredentials()` à sua política de CORS. Não é possível usar `.AllowAnyOrigin()` ou `.WithOrigins("*")` com esta opção

### <a name="response-code-413"></a>Código de resposta 413

Código de status HTTP 413-o conteúdo é muito grande

Isso geralmente é causado por ter um token de acesso acima de 4K.

* Se estiver usando o SignalR serviço do Azure, reduza o tamanho do token Personalizando as declarações sendo enviadas por meio do serviço com:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Falhas de rede transitórias

Falhas de rede transitórias podem fechar a SignalR conexão. O servidor pode interpretar a conexão fechada como uma desconexão de cliente normal. Para obter mais informações sobre por que um cliente desconectado nesses casos [coleta logs do cliente e do servidor](xref:signalr/diagnostics).