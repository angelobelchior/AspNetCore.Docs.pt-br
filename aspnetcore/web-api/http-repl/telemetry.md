---
title: Telemetria HTTP REPL
author: scottaddie
description: Saiba mais sobre a telemetria coletada pelo REPL HTTP.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
no-loc:
- appsettings.json
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502014"
---
# <a name="http-repl-telemetry"></a>Telemetria HTTP REPL

O [loop Read-eval-Print (repl) http](xref:web-api/http-repl) inclui um recurso de telemetria que coleta dados de uso. É importante que a equipe de REPL HTTP entenda como a ferramenta é usada para que possa ser melhorada.

## <a name="how-to-opt-out"></a>Como recusar

O recurso de telemetria HTTP REPL é habilitado por padrão. Para recusar o recurso de telemetria, defina a variável de ambiente `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` como `1` ou `true`.

## <a name="disclosure"></a>Divulgação

HttpRepl exibe um texto semelhante ao seguinte quando você executa a ferramenta pela primeira vez. O texto pode variar um pouco dependendo da versão da ferramenta que você está executando. Essa experiência de “primeira execução” é como a Microsoft notifica você sobre a coleta de dados.

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a>Pontos de dados

O recurso telemetria não:

* Coletar dados pessoais, como nomes de acessados, endereços de email ou URLs.
* Verifique suas solicitações ou respostas HTTP.

Os dados são enviados com segurança aos servidores da Microsoft e mantidos sob acesso restrito.

A proteção de sua privacidade é importante para nós. Se você suspeitar de que o recurso de telemetria está coletando dados confidenciais ou se os dados estiverem sendo inseguros ou indevidamente manipulados, execute uma das seguintes ações:

* Arquivo de um problema no repositório [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .
* Envie um email para [dotnet@microsoft.com](mailto:dotnet@microsoft.com) para investigação.

O recurso telemetria coleta os dados a seguir.

| Versões do SDK do .NET | Dados |
|--------------|------|
| >= 5,0        | Carimbo de data/hora da invocação. |
| >= 5,0        | Endereço IP de três octetos usado para determinar a localização geográfica. |
| >= 5,0        | Sistema operacional e versão. |
| >= 5,0        | ID de tempo de execução (RID) em que a ferramenta está sendo executada. |
| >= 5,0        | Se a ferramenta está sendo executada em um contêiner. |
| >= 5,0        | Endereço MAC (controle de acesso à mídia) com hash: um ID exclusivo e com hash criptográfico (SHA256) para um computador. |
| >= 5,0        | Versão do kernel. |
| >= 5,0        | Versão de REPL HTTP. |
| >= 5,0        | Se a ferramenta foi iniciada `help` com `run` argumentos,, ou `connect` . Os valores de argumento reais não são coletados. |
| >= 5,0        | Comando invocado (por exemplo, `get` ) e se foi bem-sucedido. |
| >= 5,0        | Para o `connect` comando, se os `root` `base` argumentos, ou `openapi` foram fornecidos. Os valores de argumento reais não são coletados. |
| >= 5,0        | Para o `pref` comando, se um `get` ou `set` foi emitido e qual preferência foi acessada. Se não for uma preferência conhecida, o nome será com hash. O valor não é coletado. |
| >= 5,0        | Para o `set header` comando, o nome do cabeçalho que está sendo definido. Se não for um cabeçalho bem conhecido, o nome será com hash. O valor não é coletado. |
| >= 5,0        | Para o `connect` comando, se um caso especial para `dotnet new webapi` foi usado e, se ele foi ignorado por meio de preferência. |
| >= 5,0        | Para todos os comandos HTTP (por exemplo, GET, POST, PUT), se cada uma das opções foi especificada. Os valores das opções não são coletados. |

## <a name="additional-resources"></a>Recursos adicionais

* [Telemetria do SDK do .NET Core](/dotnet/core/tools/telemetry)
* [CLI do .NET Core dados de telemetria](https://dotnet.microsoft.com/platform/telemetry)
