---
title: Pacote de hospedagem
author: rick-anderson
description: Saiba como configurar o pacote de hospedagem do .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343631"
---
# <a name="the-net-core-hosting-bundle"></a>O pacote de hospedagem do .NET Core

O pacote de hospedagem do .NET Core é um instalador para o tempo de execução do .NET Core e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). O pacote permite que ASP.NET Core aplicativos sejam executados com o IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Instalar o pacote de hospedagem do .NET Core

> [!IMPORTANT]
> Se o pacote de hospedagem for instalado antes do IIS, a instalação do pacote deverá ser reparada. Execute o instalador do pacote de hospedagem novamente depois de instalar o IIS.
>
> Se o pacote de hospedagem for instalado após a instalação da versão de 64 bits (x64) do .NET Core, os SDKs poderão parecer estar ausentes ([Nenhum SDK do .NET Core foi detectado](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Para resolver o problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

## <a name="direct-download-current-version"></a>Download direto (versão atual)

Baixe o instalador usando o seguinte link:

[Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a>ReVisual C++ requisito redistribuível

Em versões mais antigas do Windows, por exemplo, Windows Server 2012 R2, instale o Visual Studio C++ 2015, 2017, 2019 redistribuível. Caso contrário, uma mensagem de erro confusa no log de eventos do Windows relata que `The data is the error.`

Redistribuível x64 versus [C++ atual](https://aka.ms/vs/16/release/vc_redist.x64.exe) 
 [Redistribuível x86 vs C++ atual](https://aka.ms/vs/16/release/vc_redist.x86.exe)

## <a name="earlier-versions-of-the-installer"></a>Versões anteriores do instalador

Para obter uma versão anterior do instalador:

1. Navegue até a página [baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Selecione a versão do .NET Core desejada.
1. Na coluna **Executar aplicativos – runtime** , localize a linha da versão de runtime do .NET Core desejada.
1. Baixe o instalador usando o link **pacote de hospedagem** .

> [!WARNING]
> Alguns instaladores contêm versões de lançamento que atingiram o EOL (fim da vida útil) e não têm mais suporte da Microsoft. Para saber mais, confira a [política de suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="options"></a>Opções

1. Os parâmetros a seguir estão disponíveis ao executar o instalador por meio de um shell de comando do administrador:

   * `OPT_NO_ANCM=1`: Ignorar a instalação do módulo ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Ignorar a instalação do tempo de execução do .NET Core. Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Ignorar a instalação da estrutura compartilhada ASP.NET (ASP.NET Runtime). Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Ignorar a instalação de tempos de execução x86. Use esse parâmetro quando você souber que não hospedará aplicativos de 32 bits. Se houver uma possibilidade de hospedar aplicativos de 32 bits e 64 bits no futuro, não use esse parâmetro e instale ambos os runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Desabilite a verificação para usar uma configuração compartilhada do IIS quando a configuração compartilhada ( `applicationHost.config` ) estiver no mesmo computador que a instalação do IIS. *Disponível somente para instaladores do ASP.NET Core 2.2 ou Hosting Bundler posterior.* Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.

> [!NOTE]
> Para obter informações sobre a Configuração Compartilhada do IIS, consulte [Módulo do ASP.NET Core com a Configuração Compartilhada do IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="restart-iis"></a>Reiniciar o IIS

Depois que o grupo de hospedagem é instalado, uma reinicialização manual do IIS pode ser necessária. Por exemplo, as `dotnet` ferramentas da CLI (comando) podem não existir no caminho para executar processos de trabalho do IIS.

Para parar e iniciar o IIS manualmente, execute os seguintes comandos em um shell de comandos com privilégios elevados:

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versão do módulo e logs do instalador do pacote de hospedagem

Para determinar a versão do Módulo do ASP.NET Core instalado:

1. No sistema de hospedagem, navegue até `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .
1. Localize o `aspnetcorev2.dll` arquivo.
1. Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.
1. Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.

Os logs do instalador do pacote de hospedagem para o módulo são encontrados em `C:\Users\%UserName%\AppData\Local\Temp` . O arquivo é nomeado `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , em que o espaço reservado `{TIMESTAMP}` é o carimbo de data/hora do arquivo.
