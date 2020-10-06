---
title: Arquivo offline do aplicativo (app_offline.htm)
author: rick-anderson
description: Saiba como o arquivo offline do aplicativo ( `app_offline.htm` ) funciona com o módulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755232"
---
# <a name="app-offline-file-app_offlinehtm"></a>Arquivo offline do aplicativo ( `app_offline.htm` )

O arquivo offline do aplicativo ( `app_offline.htm` ) é usado pelo módulo ASP.NET Core para desligar um aplicativo.

Se um arquivo com o nome `app_offline.htm` for detectado no diretório raiz de um aplicativo, o módulo ASP.NET Core tentará desligar o aplicativo normalmente e interromperá o processamento de solicitações de entrada. Se o aplicativo ainda estiver em execução após o número de segundos definido em `shutdownTimeLimit` , o módulo ASP.NET Core interromperá o processo em execução.

Enquanto o `app_offline.htm` arquivo estiver presente, o módulo ASP.NET Core responde às solicitações enviando de volta o conteúdo do `app_offline.htm` arquivo. Quando o `app_offline.htm` arquivo é removido, a próxima solicitação inicia o aplicativo.

Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta. Por exemplo, uma conexão WebSocket pode atrasar o desligamento do aplicativo.

## <a name="locked-deployment-files"></a>Arquivos de implantação bloqueados

Os arquivos na pasta de implantação são bloqueados quando o aplicativo está em execução. Os arquivos bloqueados não podem ser substituídos durante a implantação.

`app_offline.htm` é o mecanismo principal para liberar arquivos bloqueados. `app_offline.htm` é usado pelo Implantação da Web para parar e iniciar o aplicativo corretamente.

`app_offline.htm` pode ser usado manualmente para iniciar e parar o aplicativo (requer o PowerShell 5 ou posterior):

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

No script anterior do PowerShell:

* O espaço reservado `{PATH TO APP}` é o caminho para o aplicativo.
* O `New-Item` comando para o pool de aplicativos.
* O `Remove-Item` comando inicia o pool de aplicativos.
* Os comandos entre o `New-Item` comando e o `Remove-Item` comando são fornecidos pelo desenvolvedor para implantar o aplicativo.

Os arquivos também podem ser desbloqueados interrompendo manualmente o pool de aplicativos no Gerenciador do IIS no servidor. Não use o `app_offine.htm` arquivo ao usar o Gerenciador do IIS para parar e reiniciar o pool de aplicativos.
