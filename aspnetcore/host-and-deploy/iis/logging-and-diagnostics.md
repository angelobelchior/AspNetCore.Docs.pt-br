---
title: Criação e redirecionamento de log com o módulo ASP.NET Core
author: rick-anderson
description: Configure o IIS e o módulo ASP.NET Core para capturar logs e informações de diagnóstico.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057434"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="a8fcf-103">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="a8fcf-103">Log creation and redirection</span></span>

<span data-ttu-id="a8fcf-104">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="a8fcf-105">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="a8fcf-106">O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` para fornecer permissão de gravação, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos).</span><span class="sxs-lookup"><span data-stu-id="a8fcf-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="a8fcf-107">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="a8fcf-108">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="a8fcf-109">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="a8fcf-110">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="a8fcf-111">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a8fcf-112">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a8fcf-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="a8fcf-113">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="a8fcf-114">O nome do arquivo de log é composto acrescentando o carimbo de data/hora, a ID do processo e a extensão do arquivo ( `.log` ) ao último segmento do `stdoutLogFile` caminho (normalmente `stdout` ) delimitado por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="a8fcf-115">Se o `stdoutLogFile` caminho terminar com `stdout` , um log de um aplicativo com um PID de 1934 criado em 2/5/2018 às 19:42:32 terá o nome do arquivo `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="a8fcf-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="a8fcf-116">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="a8fcf-117">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="a8fcf-118">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="a8fcf-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="a8fcf-119">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="a8fcf-120">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a8fcf-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="a8fcf-121">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="a8fcf-122">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="a8fcf-123">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="a8fcf-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="a8fcf-124">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="a8fcf-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="a8fcf-125">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="a8fcf-126">Adicione o `<handlerSettings>` elemento ao `<aspNetCore>` elemento em `web.config` .</span><span class="sxs-lookup"><span data-stu-id="a8fcf-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="a8fcf-127">A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="a8fcf-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="a8fcf-128">Todas as pastas no caminho ( `logs` no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="a8fcf-129">O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` para fornecer permissão de gravação, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos).</span><span class="sxs-lookup"><span data-stu-id="a8fcf-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="a8fcf-130">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="a8fcf-131">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="a8fcf-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="a8fcf-132">ERRO</span><span class="sxs-lookup"><span data-stu-id="a8fcf-132">ERROR</span></span>
* <span data-ttu-id="a8fcf-133">WARNING</span><span class="sxs-lookup"><span data-stu-id="a8fcf-133">WARNING</span></span>
* <span data-ttu-id="a8fcf-134">INFO</span><span class="sxs-lookup"><span data-stu-id="a8fcf-134">INFO</span></span>
* <span data-ttu-id="a8fcf-135">RASTREAMENTO</span><span class="sxs-lookup"><span data-stu-id="a8fcf-135">TRACE</span></span>

<span data-ttu-id="a8fcf-136">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="a8fcf-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="a8fcf-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="a8fcf-137">CONSOLE</span></span>
* <span data-ttu-id="a8fcf-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="a8fcf-138">EVENTLOG</span></span>
* <span data-ttu-id="a8fcf-139">FILE</span><span class="sxs-lookup"><span data-stu-id="a8fcf-139">FILE</span></span>

<span data-ttu-id="a8fcf-140">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="a8fcf-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="a8fcf-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="a8fcf-142">(Padrão: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="a8fcf-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="a8fcf-143">`ASPNETCORE_MODULE_DEBUG`: Configuração de nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="a8fcf-144">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="a8fcf-145">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-145">The size of the log isn't limited.</span></span> <span data-ttu-id="a8fcf-146">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="a8fcf-147">Consulte [configuração do módulo ASP.NET Core com `web.config` ](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) para obter um exemplo do `aspNetCore` elemento no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="a8fcf-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
