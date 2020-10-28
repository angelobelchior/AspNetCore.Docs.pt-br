---
title: 'Registro em log e diagnóstico no ASP.NET Core :::no-loc(SignalR):::'
author: anurse
description: 'Saiba como coletar diagnósticos do seu :::no-loc(SignalR)::: aplicativo ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
no-loc:
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
uid: signalr/diagnostics
ms.openlocfilehash: 890359c9e9f6c3c60f3105124f52c66b09a8a4fb
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690668"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="fd885-103">Registro em log e diagnóstico no ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="fd885-103">Logging and diagnostics in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="fd885-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="fd885-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="fd885-105">Este artigo fornece orientação para a coleta de diagnósticos do seu :::no-loc(SignalR)::: aplicativo ASP.NET Core para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="fd885-105">This article provides guidance for gathering diagnostics from your ASP.NET Core :::no-loc(SignalR)::: app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="fd885-106">Registro em log do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="fd885-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="fd885-107">Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="fd885-108">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="fd885-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="fd885-109">Como :::no-loc(SignalR)::: o faz parte do ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd885-109">Since :::no-loc(SignalR)::: is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="fd885-110">Na configuração padrão, o :::no-loc(SignalR)::: registra informações muito poucas, mas isso pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="fd885-110">In the default configuration, :::no-loc(SignalR)::: logs very little information, but this can configured.</span></span> <span data-ttu-id="fd885-111">Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd885-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="fd885-112">:::no-loc(SignalR)::: usa duas categorias de agente:</span><span class="sxs-lookup"><span data-stu-id="fd885-112">:::no-loc(SignalR)::: uses two logger categories:</span></span>

* <span data-ttu-id="fd885-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`: Para logs relacionados a protocolos de Hub, ativação de hubs, invocação de métodos e outras atividades relacionadas ao Hub.</span><span class="sxs-lookup"><span data-stu-id="fd885-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="fd885-114">`Microsoft.AspNetCore.Http.Connections`: Para logs relacionados a transportes, como WebSockets, sondagem longa, eventos de Server-Sent e infraestrutura de nível baixo :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="fd885-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level :::no-loc(SignalR)::: infrastructure.</span></span>

<span data-ttu-id="fd885-115">Para habilitar logs detalhados do :::no-loc(SignalR)::: , configure os dois prefixos anteriores para o `Debug` nível em seu *appsettings.jsno* arquivo adicionando os seguintes itens à `LogLevel` subseção em `Logging` :</span><span class="sxs-lookup"><span data-stu-id="fd885-115">To enable detailed logs from :::no-loc(SignalR):::, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="fd885-116">Você também pode configurar isso no código em seu `CreateWebHostBuilder` método:</span><span class="sxs-lookup"><span data-stu-id="fd885-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="fd885-117">Se você não estiver usando a configuração baseada em JSON, defina os seguintes valores de configuração em seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="fd885-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.:::no-loc(SignalR):::` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="fd885-118">Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados.</span><span class="sxs-lookup"><span data-stu-id="fd885-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="fd885-119">Por exemplo, ao usar variáveis de ambiente, dois `_` caracteres são usados em vez do `:` (por exemplo, `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::` ).</span><span class="sxs-lookup"><span data-stu-id="fd885-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`).</span></span>

<span data-ttu-id="fd885-120">É recomendável usar o `Debug` nível ao coletar diagnósticos mais detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="fd885-121">O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="fd885-122">Acessar logs do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="fd885-122">Access server-side logs</span></span>

<span data-ttu-id="fd885-123">A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.</span><span class="sxs-lookup"><span data-stu-id="fd885-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="fd885-124">Como um aplicativo de console fora do IIS</span><span class="sxs-lookup"><span data-stu-id="fd885-124">As a console app outside IIS</span></span>

<span data-ttu-id="fd885-125">Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console) deverá ser habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="fd885-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="fd885-126">:::no-loc(SignalR)::: os logs serão exibidos no console do.</span><span class="sxs-lookup"><span data-stu-id="fd885-126">:::no-loc(SignalR)::: logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="fd885-127">Dentro de IIS Express do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd885-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="fd885-128">O Visual Studio exibe a saída de log na janela **saída** .</span><span class="sxs-lookup"><span data-stu-id="fd885-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="fd885-129">Selecione a opção de menu suspenso do **ASP.NET Core Web Server** .</span><span class="sxs-lookup"><span data-stu-id="fd885-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="fd885-130">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="fd885-130">Azure App Service</span></span>

<span data-ttu-id="fd885-131">Habilite a opção de **log do aplicativo (Filesystem)** na seção **logs de diagnóstico** do portal do serviço Azure app e configure o **nível** como `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="fd885-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="fd885-132">Os logs devem estar disponíveis no serviço de **streaming de log** e nos logs no sistema de arquivos do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="fd885-133">Para obter mais informações, consulte [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="fd885-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="fd885-134">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="fd885-134">Other environments</span></span>

<span data-ttu-id="fd885-135">Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows), consulte <xref:fundamentals/logging/index> para obter mais informações sobre como configurar provedores de log adequados para o ambiente.</span><span class="sxs-lookup"><span data-stu-id="fd885-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="fd885-136">Log de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fd885-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="fd885-137">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="fd885-138">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="fd885-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="fd885-139">Ao usar o cliente JavaScript, você pode configurar as opções de log usando o `configureLogging` método em `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fd885-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="fd885-140">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="fd885-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fd885-141">A tabela a seguir mostra os níveis de log disponíveis para o cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fd885-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="fd885-142">Definir o nível de log como um desses valores habilita o registro em log nesse nível e todos os níveis acima dele na tabela.</span><span class="sxs-lookup"><span data-stu-id="fd885-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="fd885-143">Nível</span><span class="sxs-lookup"><span data-stu-id="fd885-143">Level</span></span> | <span data-ttu-id="fd885-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="fd885-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="fd885-145">Nenhuma mensagem é registrada em log.</span><span class="sxs-lookup"><span data-stu-id="fd885-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="fd885-146">Mensagens que indicam uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="fd885-147">Mensagens que indicam uma falha na operação atual.</span><span class="sxs-lookup"><span data-stu-id="fd885-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="fd885-148">Mensagens que indicam um problema não fatal.</span><span class="sxs-lookup"><span data-stu-id="fd885-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="fd885-149">Mensagens informativas.</span><span class="sxs-lookup"><span data-stu-id="fd885-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="fd885-150">Mensagens de diagnóstico úteis para depuração.</span><span class="sxs-lookup"><span data-stu-id="fd885-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="fd885-151">Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos.</span><span class="sxs-lookup"><span data-stu-id="fd885-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="fd885-152">Depois de configurar o detalhamento, os logs serão gravados no console do navegador (ou na saída padrão em um aplicativo NodeJS).</span><span class="sxs-lookup"><span data-stu-id="fd885-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="fd885-153">Se você quiser enviar logs para um sistema de registro em log personalizado, poderá fornecer um objeto JavaScript que implementa a `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="fd885-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="fd885-154">O único método que precisa ser implementado é `log` , que usa o nível do evento e a mensagem associada ao evento.</span><span class="sxs-lookup"><span data-stu-id="fd885-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="fd885-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fd885-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="fd885-156">Log de cliente .NET</span><span class="sxs-lookup"><span data-stu-id="fd885-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="fd885-157">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="fd885-158">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="fd885-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="fd885-159">Para obter logs do cliente .NET, você pode usar o `ConfigureLogging` método em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="fd885-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="fd885-160">Isso funciona da mesma maneira que o `ConfigureLogging` método em `WebHostBuilder` e `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="fd885-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="fd885-161">Você pode configurar os mesmos provedores de log usados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd885-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="fd885-162">No entanto, você precisa instalar e habilitar manualmente os pacotes NuGet para os provedores de log individuais.</span><span class="sxs-lookup"><span data-stu-id="fd885-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="fd885-163">Para adicionar o log de cliente .NET a um :::no-loc(Blazor WebAssembly)::: aplicativo, consulte <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .</span><span class="sxs-lookup"><span data-stu-id="fd885-163">To add .NET client logging to a :::no-loc(Blazor WebAssembly)::: app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="fd885-164">Log de console</span><span class="sxs-lookup"><span data-stu-id="fd885-164">Console logging</span></span>

<span data-ttu-id="fd885-165">Para habilitar o log do console, adicione o pacote [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="fd885-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="fd885-166">Em seguida, use o `AddConsole` método para configurar o agente de log do console:</span><span class="sxs-lookup"><span data-stu-id="fd885-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="fd885-167">Depurar log da janela de saída</span><span class="sxs-lookup"><span data-stu-id="fd885-167">Debug output window logging</span></span>

<span data-ttu-id="fd885-168">Você também pode configurar logs para ir para a janela de **saída** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fd885-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="fd885-169">Instale o pacote [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) e use o `AddDebug` método:</span><span class="sxs-lookup"><span data-stu-id="fd885-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="fd885-170">Outros provedores de log</span><span class="sxs-lookup"><span data-stu-id="fd885-170">Other logging providers</span></span>

<span data-ttu-id="fd885-171">:::no-loc(SignalR)::: dá suporte a outros provedores de log, como Serilog, Seq, NLog ou qualquer outro sistema de registro em log que se integre ao `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="fd885-171">:::no-loc(SignalR)::: supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="fd885-172">Se o sistema de registro em log fornecer um `ILoggerProvider` , você poderá registrá-lo com `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="fd885-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="fd885-173">Detalhes do controle</span><span class="sxs-lookup"><span data-stu-id="fd885-173">Control verbosity</span></span>

<span data-ttu-id="fd885-174">Se você estiver fazendo logon de outros locais em seu aplicativo, alterar o nível padrão para `Debug` pode ser muito detalhado.</span><span class="sxs-lookup"><span data-stu-id="fd885-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="fd885-175">Você pode usar um filtro para configurar o nível de log para :::no-loc(SignalR)::: logs.</span><span class="sxs-lookup"><span data-stu-id="fd885-175">You can use a Filter to configure the logging level for :::no-loc(SignalR)::: logs.</span></span> <span data-ttu-id="fd885-176">Isso pode ser feito no código, praticamente da mesma forma que no servidor:</span><span class="sxs-lookup"><span data-stu-id="fd885-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="fd885-177">Rastreamentos de rede</span><span class="sxs-lookup"><span data-stu-id="fd885-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="fd885-178">Um rastreamento de rede contém todo o conteúdo de cada mensagem enviada pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd885-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="fd885-179">**Nunca** poste rastreamentos de rede brutos de aplicativos de produção para fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="fd885-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="fd885-180">Se você encontrar um problema, um rastreamento de rede pode, às vezes, fornecer muitas informações úteis.</span><span class="sxs-lookup"><span data-stu-id="fd885-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="fd885-181">Isso é particularmente útil se você pretende arquivar um problema em nosso Issue Tracker.</span><span class="sxs-lookup"><span data-stu-id="fd885-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="fd885-182">Coletar um rastreamento de rede com o Fiddler (opção preferencial)</span><span class="sxs-lookup"><span data-stu-id="fd885-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="fd885-183">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fd885-183">This method works for all apps.</span></span>

<span data-ttu-id="fd885-184">O Fiddler é uma ferramenta muito poderosa para coletar rastreamentos HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd885-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="fd885-185">Instale-o em [Telerik.com/Fiddler](https://www.telerik.com/fiddler), inicie-o e execute o aplicativo e reproduza o problema.</span><span class="sxs-lookup"><span data-stu-id="fd885-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="fd885-186">O Fiddler está disponível para o Windows e há versões beta para macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="fd885-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="fd885-187">Se você se conectar usando HTTPS, haverá algumas etapas adicionais para garantir que o Fiddler possa descriptografar o tráfego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd885-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="fd885-188">Para obter mais detalhes, consulte a [documentação do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="fd885-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="fd885-189">Depois de coletar o rastreamento, você pode exportar o rastreamento escolhendo **arquivo**  >  **salvar**  >  **todas as sessões** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="fd885-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportando todas as sessões do Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="fd885-191">Coletar um rastreamento de rede com tcpdump (somente macOS e Linux)</span><span class="sxs-lookup"><span data-stu-id="fd885-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="fd885-192">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fd885-192">This method works for all apps.</span></span>

<span data-ttu-id="fd885-193">Você pode coletar rastreamentos TCP brutos usando tcpdump executando o comando a seguir de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="fd885-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="fd885-194">Talvez seja necessário ser `root` ou prefixar o comando com `sudo` se você receber um erro de permissões:</span><span class="sxs-lookup"><span data-stu-id="fd885-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="fd885-195">Substitua `[interface]` pela interface de rede que você deseja capturar.</span><span class="sxs-lookup"><span data-stu-id="fd885-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="fd885-196">Normalmente, isso é algo como `/dev/eth0` (para a sua interface Ethernet padrão) ou `/dev/lo0` (para tráfego de localhost).</span><span class="sxs-lookup"><span data-stu-id="fd885-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="fd885-197">Para obter mais informações, consulte a `tcpdump` página do manual no sistema host.</span><span class="sxs-lookup"><span data-stu-id="fd885-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="fd885-198">Coletar um rastreamento de rede no navegador</span><span class="sxs-lookup"><span data-stu-id="fd885-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="fd885-199">Esse método só funciona para aplicativos baseados em navegador.</span><span class="sxs-lookup"><span data-stu-id="fd885-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="fd885-200">A maioria dos Ferramentas para Desenvolvedores de navegador tem uma guia de "rede" que permite capturar a atividade de rede entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="fd885-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="fd885-201">No entanto, esses rastreamentos não incluem WebSocket e Server-Sent mensagens de evento.</span><span class="sxs-lookup"><span data-stu-id="fd885-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="fd885-202">Se você estiver usando esses transportes, usar uma ferramenta como Fiddler ou TcpDump (descrita abaixo) é uma abordagem melhor.</span><span class="sxs-lookup"><span data-stu-id="fd885-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="fd885-203">Microsoft Edge e Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fd885-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="fd885-204">(As instruções são as mesmas para o Edge e para o Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="fd885-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="fd885-205">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="fd885-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="fd885-206">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="fd885-206">Click the Network Tab</span></span>
3. <span data-ttu-id="fd885-207">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="fd885-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="fd885-208">Clique no ícone salvar na barra de ferramentas para exportar o rastreamento como um arquivo "HAR":</span><span class="sxs-lookup"><span data-stu-id="fd885-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![O ícone salvar na guia rede de ferramentas de desenvolvimento do Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="fd885-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="fd885-210">Google Chrome</span></span>

1. <span data-ttu-id="fd885-211">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="fd885-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="fd885-212">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="fd885-212">Click the Network Tab</span></span>
3. <span data-ttu-id="fd885-213">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="fd885-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="fd885-214">Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar como HAR com conteúdo":</span><span class="sxs-lookup"><span data-stu-id="fd885-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opção "salvar como HAR com conteúdo" na guia rede de ferramentas de desenvolvimento do Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="fd885-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fd885-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="fd885-217">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="fd885-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="fd885-218">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="fd885-218">Click the Network Tab</span></span>
3. <span data-ttu-id="fd885-219">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="fd885-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="fd885-220">Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar tudo como HAR"</span><span class="sxs-lookup"><span data-stu-id="fd885-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opção "salvar tudo como HAR" na guia de rede de ferramentas de desenvolvimento do Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="fd885-222">Anexar arquivos de diagnóstico a problemas do GitHub</span><span class="sxs-lookup"><span data-stu-id="fd885-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="fd885-223">Você pode anexar arquivos de diagnóstico a problemas do GitHub renomeando-os para que eles tenham uma `.txt` extensão e, em seguida, arrastando-os e soltando-os no problema.</span><span class="sxs-lookup"><span data-stu-id="fd885-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="fd885-224">Não Cole o conteúdo dos arquivos de log ou dos rastreamentos de rede em um problema do GitHub.</span><span class="sxs-lookup"><span data-stu-id="fd885-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="fd885-225">Esses logs e rastreamentos podem ser bem grandes, e o GitHub geralmente os trunca.</span><span class="sxs-lookup"><span data-stu-id="fd885-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Arrastando arquivos de log para um problema do GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="fd885-227">Métricas</span><span class="sxs-lookup"><span data-stu-id="fd885-227">Metrics</span></span>

<span data-ttu-id="fd885-228">Métricas é uma representação de medidas de dados em intervalos de tempo.</span><span class="sxs-lookup"><span data-stu-id="fd885-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="fd885-229">Por exemplo, solicitações por segundo.</span><span class="sxs-lookup"><span data-stu-id="fd885-229">For example, requests per second.</span></span> <span data-ttu-id="fd885-230">Os dados de métricas permitem a observação do estado de um aplicativo em um alto nível.</span><span class="sxs-lookup"><span data-stu-id="fd885-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="fd885-231">As métricas do .NET gRPC são emitidas usando <xref:System.Diagnostics.Tracing.EventCounter> .</span><span class="sxs-lookup"><span data-stu-id="fd885-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="fd885-232">:::no-loc(SignalR)::: métricas do servidor</span><span class="sxs-lookup"><span data-stu-id="fd885-232">:::no-loc(SignalR)::: server metrics</span></span>

<span data-ttu-id="fd885-233">:::no-loc(SignalR)::: as métricas do servidor são relatadas na <xref:Microsoft.AspNetCore.Http.Connections> origem do evento.</span><span class="sxs-lookup"><span data-stu-id="fd885-233">:::no-loc(SignalR)::: server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="fd885-234">Name</span><span class="sxs-lookup"><span data-stu-id="fd885-234">Name</span></span>                    | <span data-ttu-id="fd885-235">Descrição</span><span class="sxs-lookup"><span data-stu-id="fd885-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="fd885-236">Total de conexões iniciadas</span><span class="sxs-lookup"><span data-stu-id="fd885-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="fd885-237">Total de conexões interrompidas</span><span class="sxs-lookup"><span data-stu-id="fd885-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="fd885-238">Total de conexões com tempo limite esgotado</span><span class="sxs-lookup"><span data-stu-id="fd885-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="fd885-239">Conexões atuais</span><span class="sxs-lookup"><span data-stu-id="fd885-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="fd885-240">Duração média da conexão</span><span class="sxs-lookup"><span data-stu-id="fd885-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="fd885-241">Observar métricas</span><span class="sxs-lookup"><span data-stu-id="fd885-241">Observe metrics</span></span>

<span data-ttu-id="fd885-242">[dotnet-os contadores](/dotnet/core/diagnostics/dotnet-counters) são uma ferramenta de monitoramento de desempenho para monitoramento de integridade ad hoc e investigação de desempenho de primeiro nível.</span><span class="sxs-lookup"><span data-stu-id="fd885-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="fd885-243">Monitore um aplicativo .NET com `Microsoft.AspNetCore.Http.Connections` como o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="fd885-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="fd885-244">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fd885-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="fd885-245">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fd885-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
