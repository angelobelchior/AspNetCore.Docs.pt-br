---
title: Pacote de hospedagem
author: rick-anderson
description: Saiba como configurar o pacote de hospedagem do .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058435"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="4c860-103">O pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4c860-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4c860-104">O pacote de hospedagem do .NET Core é um instalador para o tempo de execução do .NET Core e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="4c860-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4c860-105">O pacote permite que ASP.NET Core aplicativos sejam executados com o IIS.</span><span class="sxs-lookup"><span data-stu-id="4c860-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="4c860-106">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4c860-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4c860-107">Se o pacote de hospedagem for instalado antes do IIS, a instalação do pacote deverá ser reparada.</span><span class="sxs-lookup"><span data-stu-id="4c860-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="4c860-108">Execute o instalador do pacote de hospedagem novamente depois de instalar o IIS.</span><span class="sxs-lookup"><span data-stu-id="4c860-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="4c860-109">Se o pacote de hospedagem for instalado após a instalação da versão de 64 bits (x64) do .NET Core, os SDKs poderão parecer estar ausentes ([Nenhum SDK do .NET Core foi detectado](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="4c860-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4c860-110">Para resolver o problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="4c860-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="4c860-111">Download direto (versão atual)</span><span class="sxs-lookup"><span data-stu-id="4c860-111">Direct download (current version)</span></span>

<span data-ttu-id="4c860-112">Baixe o instalador usando o seguinte link:</span><span class="sxs-lookup"><span data-stu-id="4c860-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="4c860-113">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="4c860-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="4c860-114">Versões anteriores do instalador</span><span class="sxs-lookup"><span data-stu-id="4c860-114">Earlier versions of the installer</span></span>

<span data-ttu-id="4c860-115">Para obter uma versão anterior do instalador:</span><span class="sxs-lookup"><span data-stu-id="4c860-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="4c860-116">Navegue até a página [baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="4c860-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="4c860-117">Selecione a versão do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="4c860-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="4c860-118">Na coluna **Executar aplicativos – runtime** , localize a linha da versão de runtime do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="4c860-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="4c860-119">Baixe o instalador usando o link **pacote de hospedagem** .</span><span class="sxs-lookup"><span data-stu-id="4c860-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="4c860-120">Alguns instaladores contêm versões de lançamento que atingiram o EOL (fim da vida útil) e não têm mais suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="4c860-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="4c860-121">Para saber mais, confira a [política de suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="4c860-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="4c860-122">Instalar o pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="4c860-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="4c860-123">Execute o instalador no servidor.</span><span class="sxs-lookup"><span data-stu-id="4c860-123">Run the installer on the server.</span></span> <span data-ttu-id="4c860-124">Os parâmetros a seguir estão disponíveis ao executar o instalador por meio de um shell de comando do administrador:</span><span class="sxs-lookup"><span data-stu-id="4c860-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="4c860-125">`OPT_NO_ANCM=1`: Ignorar a instalação do módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c860-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="4c860-126">`OPT_NO_RUNTIME=1`: Ignorar a instalação do tempo de execução do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c860-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="4c860-127">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="4c860-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4c860-128">`OPT_NO_SHAREDFX=1`: Ignorar a instalação da estrutura compartilhada ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="4c860-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="4c860-129">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="4c860-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4c860-130">`OPT_NO_X86=1`: Ignorar a instalação de tempos de execução x86.</span><span class="sxs-lookup"><span data-stu-id="4c860-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="4c860-131">Use esse parâmetro quando você souber que não hospedará aplicativos de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="4c860-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="4c860-132">Se houver uma possibilidade de hospedar aplicativos de 32 bits e 64 bits no futuro, não use esse parâmetro e instale ambos os runtimes.</span><span class="sxs-lookup"><span data-stu-id="4c860-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="4c860-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Desabilite a verificação para usar uma configuração compartilhada do IIS quando a configuração compartilhada ( `applicationHost.config` ) estiver no mesmo computador que a instalação do IIS.</span><span class="sxs-lookup"><span data-stu-id="4c860-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="4c860-134">*Disponível somente para instaladores do ASP.NET Core 2.2 ou Hosting Bundler posterior.*</span><span class="sxs-lookup"><span data-stu-id="4c860-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="4c860-135">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4c860-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="4c860-136">Reinicie o sistema ou execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="4c860-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="4c860-137">A reinicialização do IIS identifica uma alteração no CAMINHO do sistema, que é uma variável de ambiente, realizada pelo instalador.</span><span class="sxs-lookup"><span data-stu-id="4c860-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="4c860-138">ASP.NET Core não adota o comportamento de roll-forward para versões de patch de pacotes de estrutura compartilhados.</span><span class="sxs-lookup"><span data-stu-id="4c860-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="4c860-139">Depois de atualizar a estrutura compartilhada instalando um novo pacote de hospedagem, reinicie o sistema ou execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="4c860-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="4c860-140">Para obter informações sobre a Configuração Compartilhada do IIS, consulte [Módulo do ASP.NET Core com a Configuração Compartilhada do IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="4c860-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4c860-141">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="4c860-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4c860-142">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="4c860-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4c860-143">No sistema de hospedagem, navegue até `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="4c860-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="4c860-144">Localize o `aspnetcorev2.dll` arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c860-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="4c860-145">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="4c860-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4c860-146">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="4c860-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4c860-147">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="4c860-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="4c860-148">O arquivo é nomeado `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , em que o espaço reservado `{TIMESTAMP}` é o carimbo de data/hora do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c860-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
