---
title: Criação e redirecionamento de log com o módulo ASP.NET Core
author: rick-anderson
description: Configure o IIS e o módulo ASP.NET Core para capturar logs e informações de diagnóstico.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 523eec53d7d21723dcf136c4e5ce299533a78cc6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91901019"
---
# <a name="log-creation-and-redirection"></a>Criação de log e redirecionamento

O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos. Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado. O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` para fornecer permissão de gravação, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos).

Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo. É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.

O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.

Não use o log de stdout para fins gerais de registro em log do aplicativo. Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado. O nome do arquivo de log é composto acrescentando o carimbo de data/hora, a ID do processo e a extensão do arquivo ( `.log` ) ao último segmento do `stdoutLogFile` caminho (normalmente `stdout` ) delimitado por sublinhados. Se o `stdoutLogFile` caminho terminar com `stdout` , um log de um aplicativo com um PID de 1934 criado em 2/5/2018 às 19:42:32 terá o nome do arquivo `stdout_20180205194132_1934.log` .

Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB. Após a inicialização, todos os logs adicionais são descartados.

O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` . Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` . A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.

Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.

Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Logs de diagnóstico avançados

O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados. Adicione o `<handlerSettings>` elemento ao `<aspNetCore>` elemento em `web.config` . A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Todas as pastas no caminho ( `logs` no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado. O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` para fornecer permissão de gravação, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos).

Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.

Níveis (na ordem do menos para o mais detalhado):

* ERRO
* WARNING
* INFO
* RASTREAMENTO

Locais (vários locais são permitidos):

* CONSOLE
* EVENTLOG
* FILE

As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Caminho para o arquivo de log de depuração. (Padrão: `aspnetcore-debug.log` )
* `ASPNETCORE_MODULE_DEBUG`: Configuração de nível de depuração.

> [!WARNING]
> **Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema. O tamanho do log não é limitado. Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.

Consulte [configuração do módulo ASP.NET Core com `web.config` ](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) para obter um exemplo do `aspNetCore` elemento no `web.config` arquivo.
