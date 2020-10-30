---
title: Ferramentas de diagnóstico de desempenho
author: mjrousos
description: Ferramentas úteis para diagnosticar problemas de desempenho em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060853"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="2ff2a-103">Ferramentas de Diagnóstico de desempenho</span><span class="sxs-lookup"><span data-stu-id="2ff2a-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="2ff2a-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="2ff2a-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="2ff2a-105">Este artigo lista as ferramentas para diagnosticar problemas de desempenho no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="2ff2a-106">Ferramentas de Diagnóstico do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ff2a-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="2ff2a-107">As [ferramentas de criação de perfil e diagnóstico](/visualstudio/profiling) internas do Visual Studio são um bom lugar para começar a investigar problemas de desempenho.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="2ff2a-108">Essas ferramentas são poderosas e convenientes de usar no ambiente de desenvolvimento do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="2ff2a-109">As ferramentas permitem a análise do uso da CPU, do uso de memória e dos eventos de desempenho em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="2ff2a-110">Ser interno facilita a criação de perfil no tempo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="2ff2a-111">Mais informações estão disponíveis na [documentação do Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="2ff2a-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="2ff2a-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="2ff2a-112">Application Insights</span></span>

<span data-ttu-id="2ff2a-113">[Application insights](/azure/application-insights/app-insights-overview) fornece dados de desempenho detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="2ff2a-114">Application Insights coleta automaticamente dados sobre taxas de resposta, taxas de falha, tempos de resposta de dependência e muito mais.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="2ff2a-115">O Application Insights dá suporte ao log de eventos personalizados e métricas específicos para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="2ff2a-116">Aplicativo Azure insights fornece várias maneiras de fornecer informações sobre aplicativos monitorados:</span><span class="sxs-lookup"><span data-stu-id="2ff2a-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="2ff2a-117">[Mapa de aplicativos](/azure/application-insights/app-insights-app-map) – ajuda a identificar gargalos de desempenho ou pontos de acesso de falha em todos os componentes de aplicativos distribuídos.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="2ff2a-118">O [Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas em valores de métricas.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="2ff2a-119">[Folha desempenho no portal Application insights](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="2ff2a-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="2ff2a-120">Mostra detalhes de desempenho para operações diferentes no aplicativo monitorado.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="2ff2a-121">Permite detalhar uma única operação para verificar todas as partes/dependências que contribuem para uma longa duração.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="2ff2a-122">O criador de perfil pode ser invocado aqui para coletar rastreamentos de desempenho sob demanda.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="2ff2a-123">[Aplicativo Azure o profiler insights](/azure/azure-monitor/app/profiler) permite a criação de perfil regular e sob demanda de aplicativos .net.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="2ff2a-124">Portal do Azure mostra rastreamentos de desempenho capturados com pilhas de chamadas e Hot Paths.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="2ff2a-125">Os arquivos de rastreamento também podem ser baixados para análise mais profunda usando o PerfView.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="2ff2a-126">Application Insights pode ser usado em vários ambientes:</span><span class="sxs-lookup"><span data-stu-id="2ff2a-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="2ff2a-127">Otimizado para funcionar no Azure.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="2ff2a-128">Funciona em produção, desenvolvimento e preparo.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="2ff2a-129">Funciona localmente no [Visual Studio](/azure/application-insights/app-insights-visual-studio) ou em outros ambientes de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="2ff2a-130">Para obter mais informações, consulte [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="2ff2a-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="2ff2a-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="2ff2a-131">PerfView</span></span>

<span data-ttu-id="2ff2a-132">O [Perfview](https://github.com/Microsoft/perfview) é uma ferramenta de análise de desempenho criada pela equipe do .net especificamente para diagnosticar problemas de desempenho do .net.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="2ff2a-133">O PerfView permite a análise do uso da CPU, do comportamento da memória e do GC, dos eventos de desempenho e do tempo do relógio de parede.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="2ff2a-134">Você pode aprender mais sobre o PerfView e como começar a usar os [tutoriais de vídeo do Perfview](https://channel9.msdn.com/Series/PerfView-Tutorial) ou lendo o guia do usuário disponível na ferramenta ou [no GitHub](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="2ff2a-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="2ff2a-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="2ff2a-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="2ff2a-136">O [Kit de ferramentas de desempenho do Windows](/windows-hardware/test/wpt/) (WPT) consiste em dois componentes: gravador de desempenho do Windows (WPR) e o Windows Performance Analyzer (WPA).</span><span class="sxs-lookup"><span data-stu-id="2ff2a-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="2ff2a-137">As ferramentas produzem perfis de desempenho detalhados de aplicativos e sistemas operacionais Windows.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="2ff2a-138">O WPT tem maneiras mais avançadas de Visualizar dados, mas sua coleta de dados é menos eficiente do que a PerfView.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="2ff2a-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="2ff2a-139">PerfCollect</span></span>

<span data-ttu-id="2ff2a-140">Embora o PerfView seja uma ferramenta de análise de desempenho útil para cenários .NET, ele só é executado no Windows para que você não possa usá-lo para coletar rastreamentos de aplicativos ASP.NET Core executados em ambientes Linux.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="2ff2a-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) é um script bash que usa ferramentas de criação de perfil do Linux nativas ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) e [LTTng](https://lttng.org/)) para coletar rastreamentos no Linux que podem ser analisados pelo Perfview.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="2ff2a-142">O PerfCollect é útil quando problemas de desempenho aparecem em ambientes Linux em que PerfView não pode ser usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="2ff2a-143">Em vez disso, o PerfCollect pode coletar rastreamentos de aplicativos .NET Core que são analisados em um computador Windows usando o PerfView.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="2ff2a-144">Mais informações sobre como instalar e começar a usar o PerfCollect estão disponíveis [no GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="2ff2a-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="2ff2a-145">Outras ferramentas de desempenho de terceiros</span><span class="sxs-lookup"><span data-stu-id="2ff2a-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="2ff2a-146">Veja a seguir uma lista de algumas ferramentas de desempenho de terceiros que são úteis na investigação de desempenho de aplicativos .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ff2a-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="2ff2a-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="2ff2a-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="2ff2a-148">[dotTrace](https://www.jetbrains.com/profiler/) e [dotMemory](https://www.jetbrains.com/dotmemory/) da [JetBrains](https://www.jetbrains.com/)</span><span class="sxs-lookup"><span data-stu-id="2ff2a-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="2ff2a-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) da Intel</span><span class="sxs-lookup"><span data-stu-id="2ff2a-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
