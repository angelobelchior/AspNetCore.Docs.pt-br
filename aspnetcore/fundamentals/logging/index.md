---
title: Como fazer registro em log no .NET Core e no ASP.NET Core
author: rick-anderson
description: Saiba como usar a estrutura de registro em log fornecida pelo pacote do NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330701"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="1ebd1-103">Como fazer registro em log no .NET Core e no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1ebd1-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1ebd1-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1ebd1-105">O .NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1ebd1-106">Este artigo mostra como usar a API de registro em log com provedores internos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1ebd1-107">A maioria dos exemplos de código mostrados neste artigo é de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="1ebd1-108">As partes específicas de log desses trechos de código se aplicam a qualquer aplicativo .NET Core que usa o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="1ebd1-109">Os modelos de aplicativo Web ASP.NET Core usam o host genérico.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="1ebd1-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="1ebd1-111">Provedores de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-111">Logging providers</span></span>

<span data-ttu-id="1ebd1-112">Os provedores de log armazenam logs, exceto para o `Console` provedor que exibe logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="1ebd1-113">Por exemplo, o provedor do Aplicativo Azure insights armazena logs no Aplicativo Azure insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="1ebd1-114">Vários provedores podem ser habilitados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="1ebd1-115">Os modelos de aplicativo Web padrão ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="1ebd1-116">Use o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="1ebd1-117">Chamada <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="1ebd1-118">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-118">Console</span></span>](#console)
  * [<span data-ttu-id="1ebd1-119">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="1ebd1-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="1ebd1-121">[EventLog](#welog): somente Windows</span><span class="sxs-lookup"><span data-stu-id="1ebd1-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="1ebd1-122">O código anterior mostra a `Program` classe criada com o ASP.NET Core modelos de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="1ebd1-123">As várias seções a seguir fornecem exemplos baseados no ASP.NET Core modelos de aplicativo Web, que usam o host genérico.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="1ebd1-124">[Aplicativos de console que não](#nhca) são de host são discutidos posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="1ebd1-125">Para substituir o conjunto padrão de provedores de log adicionados pelo `Host.CreateDefaultBuilder` , chame `ClearProviders` e adicione os provedores de log necessários.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="1ebd1-126">Por exemplo, o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-126">For example, the following code:</span></span>

* <span data-ttu-id="1ebd1-127">Chama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> para remover todas as <xref:Microsoft.Extensions.Logging.ILoggerProvider> instâncias do construtor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="1ebd1-128">Adiciona o provedor de log de [console](#console) .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="1ebd1-129">Para provedores adicionais, consulte:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-129">For additional providers, see:</span></span>

* [<span data-ttu-id="1ebd1-130">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="1ebd1-131">[Provedores de log de](#third-party-logging-providers)terceiros.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="1ebd1-132">Criar logs</span><span class="sxs-lookup"><span data-stu-id="1ebd1-132">Create logs</span></span>

<span data-ttu-id="1ebd1-133">Para criar logs, use um <xref:Microsoft.Extensions.Logging.ILogger%601> objeto da [injeção de dependência](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="1ebd1-134">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-134">The following example:</span></span>

* <span data-ttu-id="1ebd1-135">Cria um agente, `ILogger<AboutModel>` , que usa uma *categoria* de log do nome totalmente qualificado do tipo `AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="1ebd1-136">A categoria do log é uma cadeia de caracteres associada a cada log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="1ebd1-137">Chamadas <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> para log no `Information` nível.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="1ebd1-138">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="1ebd1-139">[Níveis](#log-level) e [categorias](#log-category) são explicados em mais detalhes posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="1ebd1-140">Para obter informações sobre Blazor o, consulte [criar logs no Blazor e Blazor WebAssembly ](#clib) neste documento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="1ebd1-141">[Criar logs na principal e na inicialização](#clms) mostra como criar logs no `Main` e no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="1ebd1-142">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-142">Configure logging</span></span>

<span data-ttu-id="1ebd1-143">A configuração de log é normalmente fornecida pela `Logging` seção de *appSettings*. `{Environment}` arquivos *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="1ebd1-144">O seguinte *appsettings.Development.jsno* arquivo é gerado pelos modelos de aplicativo web do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="1ebd1-145">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-145">In the preceding JSON:</span></span>

* <span data-ttu-id="1ebd1-146">As `"Default"` `"Microsoft"` categorias,, e `"Microsoft.Hosting.Lifetime"` são especificadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="1ebd1-147">A `"Microsoft"` categoria se aplica a todas as categorias que começam com `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="1ebd1-148">Por exemplo, essa configuração se aplica à `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` categoria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="1ebd1-149">A `"Microsoft"` categoria registra no nível de log `Warning` e superior.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="1ebd1-150">A `"Microsoft.Hosting.Lifetime"` categoria é mais específica do que a `"Microsoft"` categoria, portanto, a `"Microsoft.Hosting.Lifetime"` categoria registra em nível de log "informações" e superior.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="1ebd1-151">Um provedor de log específico não é especificado, portanto `LogLevel` se aplica a todos os provedores de log habilitados, exceto para o [log de eventos do Windows](#welog).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="1ebd1-152">A `Logging` propriedade pode ter <xref:Microsoft.Extensions.Logging.LogLevel> e registrar as propriedades do provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="1ebd1-153">O `LogLevel` especifica o [nível](#log-level) mínimo para registrar em log para as categorias selecionadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1ebd1-154">No JSON anterior, `Information` e `Warning` os níveis de log são especificados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="1ebd1-155">`LogLevel`indica a severidade do log e varia de 0 a 6:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="1ebd1-156">`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 e `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="1ebd1-157">Quando um `LogLevel` é especificado, o registro em log é habilitado para mensagens no nível especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="1ebd1-158">No JSON anterior, a `Default` categoria é registrada para o `Information` e superior.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="1ebd1-159">Por exemplo,,, `Information` `Warning` `Error` e `Critical` as mensagens são registradas em log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="1ebd1-160">Se não `LogLevel` for especificado, o log padrão será o `Information` nível.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="1ebd1-161">Para obter mais informações, consulte [níveis de log](#llvl).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="1ebd1-162">Uma propriedade de provedor pode especificar uma `LogLevel` propriedade.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="1ebd1-163">`LogLevel`em um provedor especifica os níveis de log para esse provedor e substitui as configurações de log que não são do provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="1ebd1-164">Considere o seguinte *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="1ebd1-165">Configurações em `Logging.{providername}.LogLevel` configurações de substituição no `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="1ebd1-166">No JSON anterior, o `Debug` nível de log padrão do provedor é definido como `Information` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="1ebd1-167">A configuração anterior especifica o `Information` nível de log para cada `Logging:Debug:` categoria, exceto `Microsoft.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="1ebd1-168">Quando uma categoria específica é listada, a categoria específica substitui a categoria padrão.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="1ebd1-169">No JSON anterior, as `Logging:Debug:LogLevel` categorias `"Microsoft.Hosting"` e `"Default"` substituem as configurações em`Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="1ebd1-170">O nível de log mínimo pode ser especificado para qualquer um dos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="1ebd1-171">Provedores específicos: por exemplo,`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="1ebd1-172">Categorias específicas: por exemplo,`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="1ebd1-173">Todos os provedores e todas as categorias:`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="1ebd1-174">Todos os logs abaixo do nível mínimo ***não***são:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="1ebd1-175">Passado para o provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-175">Passed to the provider.</span></span>
* <span data-ttu-id="1ebd1-176">Registrado ou exibido.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-176">Logged or displayed.</span></span>

<span data-ttu-id="1ebd1-177">Para suprimir todos os logs, especifique [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="1ebd1-178">`LogLevel.None`tem um valor de 6, que é maior que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="1ebd1-179">Se um provedor oferecer suporte a [escopos de log](#logscopes), `IncludeScopes` indica se eles estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1ebd1-180">Para obter mais informações, consulte [escopos de log](#logscopes)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="1ebd1-181">O seguinte *appsettings.jsno* arquivo contém todos os provedores habilitados por padrão:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="1ebd1-182">Na amostra anterior:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-182">In the preceding sample:</span></span>

* <span data-ttu-id="1ebd1-183">As categorias e os níveis não são valores sugeridos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="1ebd1-184">O exemplo é fornecido para mostrar todos os provedores padrão.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="1ebd1-185">Configurações em `Logging.{providername}.LogLevel` configurações de substituição no `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="1ebd1-186">Por exemplo, o nível em `Debug.LogLevel.Default` substitui o nível em `LogLevel.Default` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="1ebd1-187">Cada *alias* de provedor padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="1ebd1-188">Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="1ebd1-189">Os aliases de provedores internos são:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="1ebd1-190">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-190">Console</span></span>
  * <span data-ttu-id="1ebd1-191">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-191">Debug</span></span>
  * <span data-ttu-id="1ebd1-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-192">EventSource</span></span>
  * <span data-ttu-id="1ebd1-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ebd1-193">EventLog</span></span>
  * <span data-ttu-id="1ebd1-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1ebd1-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="1ebd1-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ebd1-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="1ebd1-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="1ebd1-197">Definir o nível de log por linha de comando, variáveis de ambiente e outras configurações</span><span class="sxs-lookup"><span data-stu-id="1ebd1-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="1ebd1-198">O nível de log pode ser definido por qualquer um dos [provedores de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1ebd1-199">Os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-199">The following commands:</span></span>

* <span data-ttu-id="1ebd1-200">Defina a chave de ambiente `Logging:LogLevel:Microsoft` para um valor de `Information` no Windows.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="1ebd1-201">Teste as configurações ao usar um aplicativo criado com o ASP.NET Core modelos de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="1ebd1-202">O `dotnet run` comando deve ser executado no diretório do projeto depois de usar o `set` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="1ebd1-203">A configuração de ambiente anterior:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-203">The preceding environment setting:</span></span>

* <span data-ttu-id="1ebd1-204">É definido apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="1ebd1-205">Não é lido por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="1ebd1-206">O comando [setx](/windows-server/administration/windows-commands/setx) a seguir também define a chave de ambiente e o valor no Windows.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="1ebd1-207">Ao contrário `set` de, `setx` as configurações são persistidas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="1ebd1-208">A `/M` opção define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="1ebd1-209">Se `/M` não for usado, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="1ebd1-210">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="1ebd1-211">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="1ebd1-212">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="1ebd1-213">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-213">Exposed as environment variables.</span></span>

<span data-ttu-id="1ebd1-214">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="1ebd1-215">Para obter mais informações sobre como definir ASP.NET Core valores de configuração usando variáveis de ambiente, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="1ebd1-216">Para obter informações sobre como usar outras fontes de configuração, incluindo a linha de comando, Azure Key Vault, Azure App configuração, outros formatos de arquivo e muito mais, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1ebd1-217">Como as regras de filtragem são aplicadas</span><span class="sxs-lookup"><span data-stu-id="1ebd1-217">How filtering rules are applied</span></span>

<span data-ttu-id="1ebd1-218">Quando um objeto <xref:Microsoft.Extensions.Logging.ILogger%601> é criado, o objeto <xref:Microsoft.Extensions.Logging.ILoggerFactory> seleciona uma única regra por provedor para aplicar a esse agente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1ebd1-219">Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1ebd1-220">A regra mais específica para cada par de provedor e categoria é selecionada nas regras disponíveis.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1ebd1-221">O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1ebd1-222">Selecione todas as regras que correspondem ao provedor ou seu alias.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1ebd1-223">Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1ebd1-224">Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1ebd1-225">Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1ebd1-226">Se várias regras forem selecionadas, use a **última**.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1ebd1-227">Se nenhuma regra for selecionada, use `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="1ebd1-228">Log de saída da execução de dotnet e do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ebd1-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="1ebd1-229">Os logs criados com os [provedores de log padrão](#lp) são exibidos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="1ebd1-230">No Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ebd1-230">In Visual Studio</span></span>
  * <span data-ttu-id="1ebd1-231">Na janela de saída de depuração durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="1ebd1-232">Na janela do servidor Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="1ebd1-233">Na janela do console, quando o aplicativo é executado com `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="1ebd1-234">Os logs que começam com as categorias "Microsoft" são de ASP.NET Core código do Framework.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1ebd1-235">ASP.NET Core e o código do aplicativo usam a mesma API e provedores de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="1ebd1-236">Categoria do log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-236">Log category</span></span>

<span data-ttu-id="1ebd1-237">Quando um `ILogger` objeto é criado, uma *categoria* é especificada.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="1ebd1-238">Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1ebd1-239">A cadeia de caracteres da categoria é arbitrária, mas a Convenção é usar o nome da classe.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="1ebd1-240">Por exemplo, em um controlador, o nome pode ser `"TodoApi.Controllers.TodoController"` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="1ebd1-241">O ASP.NET Core aplicativos Web usam `ILogger<T>` para obter automaticamente uma `ILogger` instância que usa o nome do tipo totalmente qualificado `T` como a categoria:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="1ebd1-242">Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="1ebd1-243">`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="1ebd1-244">Nível de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-244">Log level</span></span>

<span data-ttu-id="1ebd1-245">A tabela a seguir lista os <xref:Microsoft.Extensions.Logging.LogLevel> valores, o `Log{LogLevel}` método de extensão de conveniência e o uso sugerido:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="1ebd1-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="1ebd1-246">LogLevel</span></span> | <span data-ttu-id="1ebd1-247">Valor</span><span class="sxs-lookup"><span data-stu-id="1ebd1-247">Value</span></span> | <span data-ttu-id="1ebd1-248">Método</span><span class="sxs-lookup"><span data-stu-id="1ebd1-248">Method</span></span> | <span data-ttu-id="1ebd1-249">Descrição</span><span class="sxs-lookup"><span data-stu-id="1ebd1-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="1ebd1-250">Trace</span><span class="sxs-lookup"><span data-stu-id="1ebd1-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-251">0</span><span class="sxs-lookup"><span data-stu-id="1ebd1-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="1ebd1-252">Conter as mensagens mais detalhadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-252">Contain the most detailed messages.</span></span> <span data-ttu-id="1ebd1-253">Essas mensagens podem conter dados confidenciais do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="1ebd1-254">Essas mensagens são desabilitadas por padrão e ***não*** devem ser habilitadas na produção.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="1ebd1-255">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-256">1</span><span class="sxs-lookup"><span data-stu-id="1ebd1-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="1ebd1-257">Para depuração e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-257">For debugging and development.</span></span> <span data-ttu-id="1ebd1-258">Use com cuidado na produção devido ao alto volume.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="1ebd1-259">Informações</span><span class="sxs-lookup"><span data-stu-id="1ebd1-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-260">2</span><span class="sxs-lookup"><span data-stu-id="1ebd1-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="1ebd1-261">Rastreia o fluxo geral do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="1ebd1-262">Pode ter um valor de longo prazo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-262">May have long-term value.</span></span> |
| [<span data-ttu-id="1ebd1-263">Aviso</span><span class="sxs-lookup"><span data-stu-id="1ebd1-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-264">3</span><span class="sxs-lookup"><span data-stu-id="1ebd1-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="1ebd1-265">Para eventos anormais ou inesperados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="1ebd1-266">Normalmente inclui erros ou condições que não fazem com que o aplicativo falhe.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="1ebd1-267">Erro</span><span class="sxs-lookup"><span data-stu-id="1ebd1-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-268">4</span><span class="sxs-lookup"><span data-stu-id="1ebd1-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="1ebd1-269">Para erros e exceções que não podem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1ebd1-270">Essas mensagens indicam uma falha na operação ou solicitação atual, não uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="1ebd1-271">Crítico</span><span class="sxs-lookup"><span data-stu-id="1ebd1-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-272">5</span><span class="sxs-lookup"><span data-stu-id="1ebd1-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="1ebd1-273">Para falhas que exigem atenção imediata.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-273">For failures that require immediate attention.</span></span> <span data-ttu-id="1ebd1-274">Exemplos: cenários de perda de dados, espaço em disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="1ebd1-275">Nenhuma</span><span class="sxs-lookup"><span data-stu-id="1ebd1-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ebd1-276">6</span><span class="sxs-lookup"><span data-stu-id="1ebd1-276">6</span></span> | | <span data-ttu-id="1ebd1-277">Especifica que uma categoria de log não deve gravar nenhuma mensagem.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="1ebd1-278">Na tabela anterior, o `LogLevel` é listado da severidade mais baixa para a mais alta.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="1ebd1-279">O primeiro parâmetro do método de [log](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , indica a severidade do log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="1ebd1-280">Em vez de chamar `Log(LogLevel, ...)` , a maioria dos desenvolvedores chama os métodos de extensão do [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="1ebd1-281">Os `Log{LogLevel}` métodos de extensão [chamam o método de log e especificam o LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="1ebd1-282">Por exemplo, as duas chamadas de log a seguir são funcionalmente equivalentes e produzem o mesmo log:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="1ebd1-283">`MyLogEvents.TestItem`é a ID do evento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="1ebd1-284">`MyLogEvents`faz parte do aplicativo de exemplo e é exibido na seção [ID do evento de log](#leid) .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="1ebd1-285">O código a seguir cria os logs `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1ebd1-286">No código anterior, o primeiro `Log{LogLevel}` parâmetro, `MyLogEvents.GetItem` , é a [ID do evento de log](#leid).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="1ebd1-287">O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1ebd1-288">Os parâmetros do método são explicados na seção de [modelo de mensagem](#lmt) mais adiante neste documento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="1ebd1-289">Chame o `Log{LogLevel}` método apropriado para controlar a quantidade de saída de log gravada em um meio de armazenamento específico.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="1ebd1-290">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-290">For example:</span></span>

* <span data-ttu-id="1ebd1-291">Em produção:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-291">In production:</span></span>
  * <span data-ttu-id="1ebd1-292">O registro em log nos `Trace` `Information` níveis ou produz um alto volume de mensagens de log detalhadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1ebd1-293">Para controlar os custos e não exceder os limites de armazenamento de dados, `Trace` as mensagens de log e de `Information` nível para um armazenamento de dados de alto volume e baixo custo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="1ebd1-294">Considere limitar `Trace` e `Information` para categorias específicas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="1ebd1-295">O registro em log `Warning` por meio de `Critical` níveis deve produzir algumas mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="1ebd1-296">Os custos e os limites de armazenamento geralmente não são uma preocupação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="1ebd1-297">Alguns logs permitem mais flexibilidade em opções de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="1ebd1-298">Em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-298">In development:</span></span>
  * <span data-ttu-id="1ebd1-299">Defina como `Warning`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="1ebd1-300">Adicionar `Trace` ou `Information` mensagens ao solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="1ebd1-301">Para limitar a saída, defina `Trace` ou `Information` apenas para as categorias em investigação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="1ebd1-302">O ASP.NET Core grava logs para eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1ebd1-303">Por exemplo, considere a saída de log para:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="1ebd1-304">Um Razor aplicativo de páginas criado com os modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="1ebd1-305">Registro em log definido como`Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="1ebd1-306">Navegação para a página de privacidade:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="1ebd1-307">Os seguintes conjuntos JSON `Logging:Console:LogLevel:Microsoft:Information` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="1ebd1-308">ID de evento de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-308">Log event ID</span></span>

<span data-ttu-id="1ebd1-309">Cada log pode especificar uma *ID do evento*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1ebd1-310">O aplicativo de exemplo usa a `MyLogEvents` classe para definir IDs de evento:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1ebd1-311">Uma ID de evento associa um conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-311">An event ID associates a set of events.</span></span> <span data-ttu-id="1ebd1-312">Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1ebd1-313">O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1ebd1-314">O provedor de Depuração não mostra IDs de eventos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1ebd1-315">O provedor de console mostra IDs de evento entre colchetes após a categoria:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="1ebd1-316">Alguns provedores de log armazenam a ID do evento em um campo, que permite filtrar a ID.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="1ebd1-317">Modelo de mensagem de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="1ebd1-318">Cada API de log usa um modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-318">Each log API uses a message template.</span></span> <span data-ttu-id="1ebd1-319">O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1ebd1-320">Use nomes para os espaços reservados, não números.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1ebd1-321">A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1ebd1-322">No código a seguir, os nomes de parâmetro estão fora de sequência no modelo de mensagem:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1ebd1-323">O código anterior cria uma mensagem de log com os valores de parâmetro em sequência:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="1ebd1-324">Essa abordagem permite que os provedores de log implementem [logs semânticos ou estruturados](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="1ebd1-325">Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1ebd1-326">Isso permite que os provedores de log armazenem os valores de parâmetro como campos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1ebd1-327">Por exemplo, considere o seguinte método de agente:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1ebd1-328">Por exemplo, ao fazer logon no armazenamento de tabelas do Azure:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="1ebd1-329">Cada entidade de tabela do Azure pode ter `ID` `RequestTime` Propriedades e.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="1ebd1-330">Tabelas com propriedades simplificam consultas em dados registrados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="1ebd1-331">Por exemplo, uma consulta pode localizar todos os logs em um `RequestTime` intervalo específico sem precisar analisar o tempo de saída da mensagem de texto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="1ebd1-332">Exceções de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-332">Log exceptions</span></span>

<span data-ttu-id="1ebd1-333">Os métodos de agente têm sobrecargas que usam um parâmetro de exceção:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="1ebd1-334">O log de exceções é específico do provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="1ebd1-335">Nível de log padrão</span><span class="sxs-lookup"><span data-stu-id="1ebd1-335">Default log level</span></span>

<span data-ttu-id="1ebd1-336">Se o nível de log padrão não for definido, o valor de nível de log padrão será `Information` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="1ebd1-337">Por exemplo, considere o seguinte aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="1ebd1-338">Criado com os modelos de aplicativo Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="1ebd1-339">*appsettings.js* e *appsettings.Development.js* excluídos ou renomeados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="1ebd1-340">Com a configuração anterior, navegar até a privacidade ou home page produz muitas `Trace` `Debug` mensagens, e `Information` com `Microsoft` no nome da categoria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="1ebd1-341">O código a seguir define o nível de log padrão quando o nível de log padrão não está definido na configuração:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="1ebd1-342">Em geral, os níveis de log devem ser especificados em configuração e não em código.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="1ebd1-343">Função Filter</span><span class="sxs-lookup"><span data-stu-id="1ebd1-343">Filter function</span></span>

<span data-ttu-id="1ebd1-344">Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="1ebd1-345">O código anterior exibe os logs do console quando a categoria contém `Controller` ou `Microsoft` e o nível de log é `Information` ou superior.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="1ebd1-346">Em geral, os níveis de log devem ser especificados em configuração e não em código.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="1ebd1-347">Categorias de ASP.NET Core e EF Core</span><span class="sxs-lookup"><span data-stu-id="1ebd1-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="1ebd1-348">A tabela a seguir contém algumas categorias usadas por ASP.NET Core e Entity Framework Core, com observações sobre os logs:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="1ebd1-349">Categoria</span><span class="sxs-lookup"><span data-stu-id="1ebd1-349">Category</span></span>                            | <span data-ttu-id="1ebd1-350">Observações</span><span class="sxs-lookup"><span data-stu-id="1ebd1-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1ebd1-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1ebd1-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1ebd1-352">Diagnóstico geral de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1ebd1-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1ebd1-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1ebd1-354">Quais chaves foram consideradas, encontradas e usadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1ebd1-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1ebd1-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1ebd1-356">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-356">Hosts allowed.</span></span> |
| <span data-ttu-id="1ebd1-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1ebd1-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1ebd1-358">Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1ebd1-359">Quais assemblies de inicialização de hospedagem foram carregados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1ebd1-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1ebd1-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1ebd1-361">MVC e Razor diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1ebd1-362">Model binding, execução de filtro, compilação de exibição, seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1ebd1-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1ebd1-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1ebd1-364">Informações de correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-364">Route matching information.</span></span> |
| <span data-ttu-id="1ebd1-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1ebd1-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1ebd1-366">Respostas de início, parada e atividade da conexão.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1ebd1-367">Informações sobre o certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1ebd1-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1ebd1-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1ebd1-369">Arquivos atendidos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-369">Files served.</span></span> |
| <span data-ttu-id="1ebd1-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1ebd1-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1ebd1-371">Diagnóstico geral do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1ebd1-372">Atividade e configuração do banco de dados, detecção de alterações, migrações.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="1ebd1-373">Para exibir mais categorias na janela do console, defina **appsettings.Development.js** para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="1ebd1-374">Escopos de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-374">Log scopes</span></span>

 <span data-ttu-id="1ebd1-375">Um *escopo* pode agrupar um conjunto de operações lógicas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1ebd1-376">Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1ebd1-377">Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1ebd1-378">Um escopo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-378">A scope:</span></span>

* <span data-ttu-id="1ebd1-379">É um <xref:System.IDisposable> tipo que é retornado pelo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> método.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="1ebd1-380">Dura até ser Descartado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="1ebd1-381">Os seguintes provedores dão suporte a escopos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="1ebd1-382">AzureAppServicesFile e AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ebd1-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="1ebd1-383">Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="1ebd1-384">O JSON a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="1ebd1-385">O código a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="1ebd1-386">Em geral, o registro em log deve ser especificado em configuração e não em código.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="1ebd1-387">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-387">Built-in logging providers</span></span>

<span data-ttu-id="1ebd1-388">ASP.NET Core inclui os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="1ebd1-389">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-389">Console</span></span>](#console)
* [<span data-ttu-id="1ebd1-390">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-390">Debug</span></span>](#debug)
* [<span data-ttu-id="1ebd1-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="1ebd1-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ebd1-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="1ebd1-393">AzureAppServicesFile e AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ebd1-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="1ebd1-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="1ebd1-395">Para obter informações sobre `stdout` o e o log de depuração com o módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="1ebd1-396">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-396">Console</span></span>

<span data-ttu-id="1ebd1-397">O `Console` provedor registra a saída no console.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="1ebd1-398">Para obter mais informações sobre como exibir `Console` logs no desenvolvimento, consulte [log output of dotnet Run e Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="1ebd1-399">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-399">Debug</span></span>

<span data-ttu-id="1ebd1-400">O `Debug` provedor grava a saída de log usando a classe [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="1ebd1-401">Chamadas para `System.Diagnostics.Debug.WriteLine` gravar no `Debug` provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="1ebd1-402">No Linux, o `Debug` local do log do provedor é dependente de distribuição e pode ser um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="1ebd1-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="1ebd1-403">*/var/log/message*</span></span>
* <span data-ttu-id="1ebd1-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="1ebd1-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="1ebd1-405">Origem do Evento</span><span class="sxs-lookup"><span data-stu-id="1ebd1-405">Event Source</span></span>

<span data-ttu-id="1ebd1-406">O `EventSource` provedor grava em uma origem de evento de plataforma cruzada com o nome `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1ebd1-407">No Windows, o provedor usa o [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="1ebd1-408">ferramentas de rastreamento dotnet</span><span class="sxs-lookup"><span data-stu-id="1ebd1-408">dotnet trace tooling</span></span>

<span data-ttu-id="1ebd1-409">A ferramenta [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) é uma ferramenta global da CLI de plataforma cruzada que habilita a coleta de rastreamentos do .NET Core de um processo em execução.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="1ebd1-410">A ferramenta coleta <xref:Microsoft.Extensions.Logging.EventSource> dados do provedor usando um <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="1ebd1-411">Consulte [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) para obter instruções de instalação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="1ebd1-412">Use as ferramentas de rastreamento dotnet para coletar um rastreamento de um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="1ebd1-413">Execute o aplicativo com o `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="1ebd1-414">Determine o identificador do processo (PID) do aplicativo .NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="1ebd1-415">No Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="1ebd1-416">Gerenciador de tarefas (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="1ebd1-417">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="1ebd1-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="1ebd1-418">Comando do PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="1ebd1-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="1ebd1-419">No Linux, use o [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="1ebd1-420">Localize o PID do processo que tem o mesmo nome que o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="1ebd1-421">Execute o `dotnet trace` comando.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="1ebd1-422">Sintaxe de comando geral:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="1ebd1-423">Ao usar um shell de comando do PowerShell, coloque o `--providers` valor entre aspas simples ( `'` ):</span><span class="sxs-lookup"><span data-stu-id="1ebd1-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="1ebd1-424">Em plataformas não Windows, adicione a `-f speedscope` opção para alterar o formato do arquivo de rastreamento de saída para `speedscope` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="1ebd1-425">Palavra-chave</span><span class="sxs-lookup"><span data-stu-id="1ebd1-425">Keyword</span></span> | <span data-ttu-id="1ebd1-426">Descrição</span><span class="sxs-lookup"><span data-stu-id="1ebd1-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="1ebd1-427">1</span><span class="sxs-lookup"><span data-stu-id="1ebd1-427">1</span></span>       | <span data-ttu-id="1ebd1-428">Registre os eventos meta sobre o `LoggingEventSource` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="1ebd1-429">Não registra eventos de `ILogger` ).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="1ebd1-430">2</span><span class="sxs-lookup"><span data-stu-id="1ebd1-430">2</span></span>       | <span data-ttu-id="1ebd1-431">Ativa o `Message` evento quando `ILogger.Log()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1ebd1-432">Fornece informações em uma maneira programática (não formatada).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="1ebd1-433">4</span><span class="sxs-lookup"><span data-stu-id="1ebd1-433">4</span></span>       | <span data-ttu-id="1ebd1-434">Ativa o `FormatMessage` evento quando `ILogger.Log()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1ebd1-435">Fornece a versão de cadeia de caracteres formatada das informações.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="1ebd1-436">8</span><span class="sxs-lookup"><span data-stu-id="1ebd1-436">8</span></span>       | <span data-ttu-id="1ebd1-437">Ativa o `MessageJson` evento quando `ILogger.Log()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1ebd1-438">Fornece uma representação JSON dos argumentos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="1ebd1-439">Evento em nível</span><span class="sxs-lookup"><span data-stu-id="1ebd1-439">Event Level</span></span> | <span data-ttu-id="1ebd1-440">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="1ebd1-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="1ebd1-441">0</span><span class="sxs-lookup"><span data-stu-id="1ebd1-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="1ebd1-442">1</span><span class="sxs-lookup"><span data-stu-id="1ebd1-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="1ebd1-443">2</span><span class="sxs-lookup"><span data-stu-id="1ebd1-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="1ebd1-444">3</span><span class="sxs-lookup"><span data-stu-id="1ebd1-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="1ebd1-445">4</span><span class="sxs-lookup"><span data-stu-id="1ebd1-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="1ebd1-446">5</span><span class="sxs-lookup"><span data-stu-id="1ebd1-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="1ebd1-447">`FilterSpecs`entradas para `{Logger Category}` e `{Event Level}` representam condições de filtragem de log adicionais.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="1ebd1-448">Separe `FilterSpecs` as entradas com um ponto e vírgula ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="1ebd1-449">Exemplo usando um shell de comando do Windows (**sem** aspas simples em volta do `--providers` valor):</span><span class="sxs-lookup"><span data-stu-id="1ebd1-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="1ebd1-450">O comando anterior é ativado:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-450">The preceding command activates:</span></span>

   * <span data-ttu-id="1ebd1-451">O agente de log de origem do evento para produzir cadeias de caracteres formatadas ( `4` ) para erros ( `2` ).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="1ebd1-452">`Microsoft.AspNetCore.Hosting`registro em log no `Informational` nível de log ( `4` ).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="1ebd1-453">Pare as ferramentas de rastreamento dotnet pressionando a tecla Enter ou CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="1ebd1-454">O rastreamento é salvo com o nome *trace. NetTrace* na pasta em que o `dotnet trace` comando é executado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="1ebd1-455">Abra o rastreamento com [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="1ebd1-456">Abra o arquivo *trace. NetTrace* e explore os eventos de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="1ebd1-457">Se o aplicativo não criar o host com o `CreateDefaultBuilder` , adicione o [provedor de origem do evento](#event-source-provider) à configuração de log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="1ebd1-458">Para obter mais informações, veja:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-458">For more information, see:</span></span>

* <span data-ttu-id="1ebd1-459">[Rastreamento do utilitário de análise de desempenho (dotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (documentação do .NET Core)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="1ebd1-460">[Rastreamento do utilitário de análise de desempenho (dotNet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentação do repositório do GitHub de dotnet/diagnóstico)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="1ebd1-461">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (navegador de API .net)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="1ebd1-462">[Fonte de referência LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): para obter a fonte de referência para uma versão diferente, altere a ramificação para `release/{Version}` , onde `{Version}` é a versão do ASP.NET Core desejado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="1ebd1-463">[Perfview](#perfview): útil para exibir rastreamentos de origem do evento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="1ebd1-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="1ebd1-464">Perfview</span></span>

<span data-ttu-id="1ebd1-465">Use o [utilitário Perfview](https://github.com/Microsoft/perfview) para coletar e exibir logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1ebd1-466">Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1ebd1-467">Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1ebd1-468">Não perca o `*` no início da cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="1ebd1-469">EventLog do Windows</span><span class="sxs-lookup"><span data-stu-id="1ebd1-469">Windows EventLog</span></span>

<span data-ttu-id="1ebd1-470">O `EventLog` provedor envia a saída de log para o log de eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="1ebd1-471">Ao contrário dos outros provedores, o `EventLog` provedor ***não*** herda as configurações padrão de não provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="1ebd1-472">Se `EventLog` as configurações de log não forem especificadas, elas serão [padronizadas como LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="1ebd1-473">Para registrar em log eventos inferiores <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> a, defina explicitamente o nível de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="1ebd1-474">O exemplo a seguir define o nível de log padrão do log de eventos como <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="1ebd1-475">[Sobrecargas de autoeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) podem passar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1ebd1-476">Se `null` ou não for especificado, as seguintes configurações padrão serão usadas:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1ebd1-477">`LogName`: "Aplicativo"</span><span class="sxs-lookup"><span data-stu-id="1ebd1-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="1ebd1-478">`SourceName`: "Tempo de execução do .NET"</span><span class="sxs-lookup"><span data-stu-id="1ebd1-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="1ebd1-479">`MachineName`: O nome do computador local é usado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="1ebd1-480">O código a seguir altera o `SourceName` do valor padrão de `".NET Runtime"` para `MyLogs` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="1ebd1-481">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="1ebd1-481">Azure App Service</span></span>

<span data-ttu-id="1ebd1-482">O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="1ebd1-483">O pacote do provedor não está incluído na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="1ebd1-484">Para usar o provedor, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="1ebd1-485">Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="1ebd1-486">Quando implantado no serviço Azure App, o aplicativo usa as configurações na seção [logs do serviço de aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) da página Serviço de **aplicativo** da portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1ebd1-487">Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1ebd1-488">**Log de aplicativo (Sistema de Arquivos)**</span><span class="sxs-lookup"><span data-stu-id="1ebd1-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1ebd1-489">**Log de aplicativo (Blob)**</span><span class="sxs-lookup"><span data-stu-id="1ebd1-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1ebd1-490">O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1ebd1-491">O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1ebd1-492">O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1ebd1-493">Esse provedor só registra quando o projeto é executado no ambiente do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1ebd1-494">Fluxo de log do Azure</span><span class="sxs-lookup"><span data-stu-id="1ebd1-494">Azure log streaming</span></span>

<span data-ttu-id="1ebd1-495">O Azure log streaming dá suporte à exibição da atividade de log em tempo real em:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="1ebd1-496">O servidor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-496">The app server</span></span>
* <span data-ttu-id="1ebd1-497">Do servidor Web</span><span class="sxs-lookup"><span data-stu-id="1ebd1-497">The web server</span></span>
* <span data-ttu-id="1ebd1-498">De uma solicitação de rastreio com falha</span><span class="sxs-lookup"><span data-stu-id="1ebd1-498">Failed request tracing</span></span>

<span data-ttu-id="1ebd1-499">Para configurar o fluxo de log do Azure:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1ebd1-500">Navegue até a página de **logs do serviço de aplicativo** na página do portal do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="1ebd1-501">Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1ebd1-502">Escolha o **Nível** de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-502">Choose the log **Level**.</span></span> <span data-ttu-id="1ebd1-503">Essa configuração se aplica somente ao streaming de log do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="1ebd1-504">Navegue até a página **fluxo de log** para exibir os logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="1ebd1-505">As mensagens registradas são registradas com a `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="1ebd1-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-506">Azure Application Insights</span></span>

<span data-ttu-id="1ebd1-507">O pacote do provedor [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava logs em [aplicativo Azure insights](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="1ebd1-508">O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1ebd1-509">Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1ebd1-510">O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1ebd1-511">Se você usar esse pacote, não precisará instalar o pacote de provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1ebd1-512">O pacote [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) é para o ASP.NET 4. x, não ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="1ebd1-513">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1ebd1-514">Visão geral do Application Insights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1ebd1-515">[Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1ebd1-516">[ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1ebd1-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs) (Adaptadores de registro em log do Application Insights).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1ebd1-518">[Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1ebd1-519">Provedores de log de terceiros</span><span class="sxs-lookup"><span data-stu-id="1ebd1-519">Third-party logging providers</span></span>

<span data-ttu-id="1ebd1-520">Estruturas de log de terceiros que funcionam com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1ebd1-521">[elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1ebd1-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1ebd1-523">[JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1ebd1-524">[KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1ebd1-525">[Log4net](https://logging.apache.org/log4net/) ([repositório GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1ebd1-526">[Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1ebd1-527">[NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1ebd1-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repositório do GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="1ebd1-529">[Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1ebd1-530">[Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1ebd1-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1ebd1-532">Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1ebd1-533">Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1ebd1-534">Adicione um pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1ebd1-535">Chame um `ILoggerFactory` método de extensão fornecido pela estrutura de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1ebd1-536">Para saber mais, consulte a documentação de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1ebd1-537">Não há suporte para provedores de log de terceiros na Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="1ebd1-538">Aplicativo de console não host</span><span class="sxs-lookup"><span data-stu-id="1ebd1-538">Non-host console app</span></span>

<span data-ttu-id="1ebd1-539">Para obter um exemplo de como usar o host genérico em um aplicativo de console não Web, consulte o arquivo *Program.cs* do [aplicativo de exemplo de tarefas em segundo plano](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="1ebd1-540">O código de registro em log de aplicativos sem host genérico difere na maneira como os [provedores são adicionados](#add-providers) e como os [agentes são criados](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="1ebd1-541">Provedores de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-541">Logging providers</span></span>

<span data-ttu-id="1ebd1-542">Em um aplicativo de console não host, chame o método de extensão `Add{provider name}` do provedor ao criar um `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="1ebd1-543">Criar logs</span><span class="sxs-lookup"><span data-stu-id="1ebd1-543">Create logs</span></span>

<span data-ttu-id="1ebd1-544">Para criar logs, use um objeto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1ebd1-545">Use o `LoggerFactory` para criar um `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1ebd1-546">O exemplo a seguir cria um agente de log com `LoggingConsoleApp.Program` como a categoria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="1ebd1-547">Nos exemplos do ASP.NET CORE a seguir, o agente é usado para criar logs com `Information` o nível.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1ebd1-548">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="1ebd1-549">[Níveis](#log-level) e [categorias](#log-category) são explicados com mais detalhes neste documento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="1ebd1-550">Log durante a construção do host</span><span class="sxs-lookup"><span data-stu-id="1ebd1-550">Log during host construction</span></span>

<span data-ttu-id="1ebd1-551">Não há suporte direto para o registro em log durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1ebd1-552">No entanto, um agente separado pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-552">However, a separate logger can be used.</span></span> <span data-ttu-id="1ebd1-553">No exemplo a seguir, um agente de log do [Serilog](https://serilog.net/) é usado para fazer logon `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="1ebd1-554">`AddSerilog`usa a configuração estática especificada em `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="1ebd1-555">Configurar um serviço que depende do ILogger</span><span class="sxs-lookup"><span data-stu-id="1ebd1-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="1ebd1-556">A injeção de construtor de um agente em `Startup` funciona em versões anteriores do ASP.NET Core porque um contêiner de DI separado é criado para o host da Web.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="1ebd1-557">Para obter informações sobre por que apenas um contêiner é criado para o host genérico, consulte o [anúncio de alteração da falha](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="1ebd1-558">Para configurar um serviço que depende do `ILogger<T>` , use a injeção de construtor ou forneça um método de fábrica.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="1ebd1-559">A abordagem do método de fábrica é recomendada somente se não há nenhuma outra opção.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="1ebd1-560">Por exemplo, considere um serviço que precisa de uma `ILogger<T>` instância fornecida por di:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="1ebd1-561">O código realçado anterior é um [Func](/dotnet/api/system.func-2) que é executado na primeira vez que o contêiner de di precisa construir uma instância do `MyService` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="1ebd1-562">Você pode acessar qualquer um dos serviços registrados dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="1ebd1-563">Criar logs no principal</span><span class="sxs-lookup"><span data-stu-id="1ebd1-563">Create logs in Main</span></span>

<span data-ttu-id="1ebd1-564">O código a seguir faz logon `Main` obtendo uma `ILogger` instância de di após a criação do host:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="1ebd1-565">Criar logs na inicialização</span><span class="sxs-lookup"><span data-stu-id="1ebd1-565">Create logs in Startup</span></span>

<span data-ttu-id="1ebd1-566">O código a seguir grava logs em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="1ebd1-567">A gravação de logs antes da conclusão da configuração do contêiner de DI no método `Startup.ConfigureServices` não é uma ação compatível:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="1ebd1-568">A injeção de agente no construtor `Startup` não é uma ação compatível.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="1ebd1-569">A injeção de agente na assinatura do método `Startup.ConfigureServices` não é uma ação compatível</span><span class="sxs-lookup"><span data-stu-id="1ebd1-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="1ebd1-570">O motivo para essa restrição é que o registro em log depende da DI e da configuração, a qual por sua vez depende da DI.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="1ebd1-571">O contêiner de DI não é configurado até `ConfigureServices` ser concluído.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="1ebd1-572">Para obter informações sobre como configurar um serviço que depende `ILogger<T>` ou por que a injeção de construtor de um agente de log `Startup` funciona em versões anteriores, consulte [configurar um serviço que depende do ILogger](#csdi)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1ebd1-573">Sem métodos de agente assíncronos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-573">No asynchronous logger methods</span></span>

<span data-ttu-id="1ebd1-574">O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1ebd1-575">Se um armazenamento de dados de log estiver lento, não grave-o diretamente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1ebd1-576">Considere gravar as mensagens de log em um repositório rápido inicialmente e, em seguida, movê-las para o armazenamento lento mais tarde.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="1ebd1-577">Por exemplo, ao fazer logon no SQL Server, não faça isso diretamente em um `Log` método, pois os `Log` métodos são síncronos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1ebd1-578">Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1ebd1-579">Para obter mais informações, consulte [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema do github.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="1ebd1-580">Alterar os níveis de log em um aplicativo em execução</span><span class="sxs-lookup"><span data-stu-id="1ebd1-580">Change log levels in a running app</span></span>

<span data-ttu-id="1ebd1-581">A API de registro em log não inclui um cenário para alterar os níveis de log enquanto um aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1ebd1-582">No entanto, alguns provedores de configuração são capazes de recarregar a configuração, o que exige um efeito imediato na configuração de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1ebd1-583">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider), recarrega a configuração de log por padrão.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="1ebd1-584">Se a configuração for alterada no código enquanto um aplicativo estiver em execução, o aplicativo poderá chamar [IConfigurationRoot. recarregar](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para atualizar a configuração de log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="1ebd1-585">ILogger e ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="1ebd1-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="1ebd1-586">As <xref:Microsoft.Extensions.Logging.ILogger%601> <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces e implementações são incluídas no SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="1ebd1-587">Eles também estão disponíveis nos seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="1ebd1-588">As interfaces estão em [Microsoft. Extensions. Logging. abstrações](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="1ebd1-589">As implementações padrão estão em [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="1ebd1-590">Aplicar regras de filtro de log no código</span><span class="sxs-lookup"><span data-stu-id="1ebd1-590">Apply log filter rules in code</span></span>

<span data-ttu-id="1ebd1-591">A abordagem preferida para definir regras de filtro de log é usando a [configuração](xref:fundamentals/configuration/index)do.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="1ebd1-592">O exemplo a seguir mostra como registrar regras de filtro no código:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="1ebd1-593">`logging.AddFilter("System", LogLevel.Debug)`Especifica a `System` categoria e o nível de log `Debug` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="1ebd1-594">O filtro é aplicado a todos os provedores porque um provedor específico não foi configurado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="1ebd1-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`especificado</span><span class="sxs-lookup"><span data-stu-id="1ebd1-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="1ebd1-596">O `Debug` provedor de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="1ebd1-597">Nível `Information` de log e superior.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="1ebd1-598">Todas as categorias que começam com `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="1ebd1-599">Criar um agente de log personalizado</span><span class="sxs-lookup"><span data-stu-id="1ebd1-599">Create a custom logger</span></span>

<span data-ttu-id="1ebd1-600">Para adicionar um agente de log personalizado, adicione um <xref:Microsoft.Extensions.Logging.ILoggerProvider> com <xref:Microsoft.Extensions.Logging.ILoggerFactory> :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="1ebd1-601">O `ILoggerProvider` cria uma ou mais `ILogger` instâncias.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="1ebd1-602">As `ILogger` instâncias são usadas pelo Framework para registrar as informações.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="1ebd1-603">Configuração de agente de log personalizado de exemplo</span><span class="sxs-lookup"><span data-stu-id="1ebd1-603">Sample custom logger configuration</span></span>

<span data-ttu-id="1ebd1-604">A amostra:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-604">The sample:</span></span>

* <span data-ttu-id="1ebd1-605">O é projetado para ser um exemplo muito básico que define a cor do console de log por ID do evento e nível de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="1ebd1-606">Os agentes geralmente não mudam de acordo com a ID do evento e não são específicos do nível de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="1ebd1-607">Cria entradas de console de cores diferentes por nível de log e ID de evento usando o seguinte tipo de configuração:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="1ebd1-608">O código anterior define o nível padrão como `Warning` e a cor como `Yellow` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="1ebd1-609">Se o `EventId` for definido como 0, registraremos em log todos os eventos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="1ebd1-610">Criar o agente de log personalizado</span><span class="sxs-lookup"><span data-stu-id="1ebd1-610">Create the custom logger</span></span>

<span data-ttu-id="1ebd1-611">O `ILogger` nome da categoria de implementação normalmente é a origem do log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="1ebd1-612">Por exemplo, o tipo em que o agente é criado:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="1ebd1-613">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-613">The preceding code:</span></span>

* <span data-ttu-id="1ebd1-614">Cria uma instância de agente por nome de categoria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="1ebd1-615">Faz `logLevel == _config.LogLevel` o check-in `IsEnabled` , de modo que cada `logLevel` um tem um agente exclusivo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="1ebd1-616">Em geral, os agentes também devem ser habilitados para todos os níveis de log mais altos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="1ebd1-617">Criar o Loggerprovider personalizado</span><span class="sxs-lookup"><span data-stu-id="1ebd1-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="1ebd1-618">O `LoggerProvider` é a classe que cria as instâncias de agente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="1ebd1-619">Talvez não seja necessário criar uma instância de agente por categoria, mas isso faz sentido para alguns agentes, como NLog ou log4net.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="1ebd1-620">Fazendo isso, você também pode escolher destinos de saída de log diferentes por categoria, se necessário:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="1ebd1-621">No código anterior, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> cria uma única instância do `ColorConsoleLogger` nome por categoria e a armazena no [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;</span><span class="sxs-lookup"><span data-stu-id="1ebd1-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="1ebd1-622">Uso e registro do agente de log personalizado</span><span class="sxs-lookup"><span data-stu-id="1ebd1-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="1ebd1-623">Registre o agente de log no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="1ebd1-624">Para o código anterior, forneça pelo menos um método de extensão para `ILoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1ebd1-625">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1ebd1-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="1ebd1-626">Os bugs de log devem ser criados no repositório [github.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1ebd1-627">Por [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1ebd1-628">O .NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1ebd1-629">Este artigo mostra como usar a API de registro em log com provedores internos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1ebd1-630">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="1ebd1-631">Adicionar provedores</span><span class="sxs-lookup"><span data-stu-id="1ebd1-631">Add providers</span></span>

<span data-ttu-id="1ebd1-632">Um provedor de log exibe ou armazena logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="1ebd1-633">Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="1ebd1-634">Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="1ebd1-635">Para adicionar um provedor, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="1ebd1-636">O código anterior requer referências a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="1ebd1-637">O modelo de projeto padrão chama o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="1ebd1-638">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-638">Console</span></span>
* <span data-ttu-id="1ebd1-639">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-639">Debug</span></span>
* <span data-ttu-id="1ebd1-640">EventSource (a partir do ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="1ebd1-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="1ebd1-641">Se você usar `CreateDefaultBuilder`, poderá substituir os provedores padrão por aqueles que preferir.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="1ebd1-642">Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="1ebd1-643">Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="1ebd1-644">Criar logs</span><span class="sxs-lookup"><span data-stu-id="1ebd1-644">Create logs</span></span>

<span data-ttu-id="1ebd1-645">Para criar logs, use um objeto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1ebd1-646">Em um aplicativo Web ou serviço hospedado, obtenha um `ILogger` da DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="1ebd1-647">Em aplicativos de console não host, use o `LoggerFactory` para criar um `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1ebd1-648">O exemplo do ASP.NET Core a seguir cria um agente de categoria `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="1ebd1-649">A *categoria* do log é uma cadeia de caracteres associada a cada log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="1ebd1-650">A instância de `ILogger<T>` cria logs que têm como a categoria o nome totalmente qualificado do tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="1ebd1-651">Nos exemplos de ASP.NET Core e aplicativo de console a seguir, o agente é usado para criar logs de nível `Information`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1ebd1-652">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="1ebd1-653">[Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="1ebd1-654">Criar logs na inicialização</span><span class="sxs-lookup"><span data-stu-id="1ebd1-654">Create logs in Startup</span></span>

<span data-ttu-id="1ebd1-655">Para gravar logs na classe `Startup`, inclua um parâmetro `ILogger` na assinatura de construtor:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="1ebd1-656">Criar logs na classe Programa</span><span class="sxs-lookup"><span data-stu-id="1ebd1-656">Create logs in the Program class</span></span>

<span data-ttu-id="1ebd1-657">Para gravar logs na classe `Program`, obtenha uma instância `ILogger` da DI:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="1ebd1-658">Não há suporte direto para o registro em log durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1ebd1-659">No entanto, um agente separado pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-659">However, a separate logger can be used.</span></span> <span data-ttu-id="1ebd1-660">No exemplo a seguir, um agente de log do [Serilog](https://serilog.net/) é usado para fazer logon `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="1ebd1-661">`AddSerilog`usa a configuração estática especificada em `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1ebd1-662">Sem métodos de agente assíncronos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-662">No asynchronous logger methods</span></span>

<span data-ttu-id="1ebd1-663">O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1ebd1-664">Se o armazenamento de dados em log estiver lento, não grave diretamente nele.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1ebd1-665">Grave as mensagens de log em um repositório rápido primeiro e, depois, mova-as para um repositório lento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="1ebd1-666">Por exemplo, se você estiver enviado logs para o SQL Server, convém não fazer isso diretamente em um método `Log`, uma vez que os métodos `Log` são síncronos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1ebd1-667">Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1ebd1-668">Para obter mais informações, consulte [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema do github.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="1ebd1-669">Configuração</span><span class="sxs-lookup"><span data-stu-id="1ebd1-669">Configuration</span></span>

<span data-ttu-id="1ebd1-670">A configuração do provedor de logs é fornecida por um ou mais provedores de sincronização:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="1ebd1-671">Formatos de arquivo (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="1ebd1-672">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-672">Command-line arguments.</span></span>
* <span data-ttu-id="1ebd1-673">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-673">Environment variables.</span></span>
* <span data-ttu-id="1ebd1-674">Objetos do .NET na memória.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="1ebd1-675">O armazenamento do [Secret Manager](xref:security/app-secrets) não criptografado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="1ebd1-676">Um repositório de usuário criptografado, como o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="1ebd1-677">Provedores personalizados (instalados ou criados).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="1ebd1-678">Por exemplo, a configuração de log geralmente é fornecida pela seção `Logging` dos arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="1ebd1-679">O exemplo a seguir mostra o conteúdo de um típico arquivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="1ebd1-680">A propriedade `Logging` pode ter `LogLevel` e propriedades do provedor de logs (o Console é mostrado).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="1ebd1-681">A propriedade `LogLevel` em `Logging` especifica o [nível](#log-level) mínimo para log nas categorias selecionadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1ebd1-682">No exemplo, as categorias `System` e `Microsoft` têm log no nível `Information`, e todas as outras no nível `Debug`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="1ebd1-683">Outras propriedades em `Logging` especificam provedores de logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="1ebd1-684">O exemplo se refere ao provedor de Console.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-684">The example is for the Console provider.</span></span> <span data-ttu-id="1ebd1-685">Se um provedor oferecer suporte a [escopos de log](#log-scopes), `IncludeScopes` indica se eles estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1ebd1-686">Uma propriedade de provedor (como `Console`, no exemplo) também pode especificar uma propriedade `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="1ebd1-687">`LogLevel` em um provedor especifica os níveis de log para esse provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="1ebd1-688">Se os níveis forem especificados em `Logging.{providername}.LogLevel`, eles substituirão o que estiver definido em `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="1ebd1-689">Por exemplo, considere o seguinte JSON:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="1ebd1-690">No JSON anterior, as `Console` configurações do provedor substituem o nível de log anterior (padrão).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="1ebd1-691">A API de registro em log não inclui um cenário para alterar os níveis de log enquanto um aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1ebd1-692">No entanto, alguns provedores de configuração são capazes de recarregar a configuração, o que exige um efeito imediato na configuração de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1ebd1-693">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider), que é adicionado pelo `CreateDefaultBuilder` para ler arquivos de configurações, recarrega a configuração de log por padrão.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="1ebd1-694">Se a configuração for alterada no código enquanto um aplicativo estiver em execução, o aplicativo poderá chamar [IConfigurationRoot. recarregar](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para atualizar a configuração de log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="1ebd1-695">Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="1ebd1-696">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-696">Sample logging output</span></span>

<span data-ttu-id="1ebd1-697">Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="1ebd1-698">Aqui está um exemplo da saída do console:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-698">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="1ebd1-699">Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="1ebd1-700">Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="1ebd1-701">Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="1ebd1-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="1ebd1-702">Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1ebd1-703">O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="1ebd1-704">O restante deste artigo explica alguns detalhes e opções para registro em log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="1ebd1-705">Pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="1ebd1-705">NuGet packages</span></span>

<span data-ttu-id="1ebd1-706">As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="1ebd1-707">Categoria do log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-707">Log category</span></span>

<span data-ttu-id="1ebd1-708">Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="1ebd1-709">Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1ebd1-710">A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="1ebd1-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="1ebd1-711">Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="1ebd1-712">Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="1ebd1-713">`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="1ebd1-714">Nível de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-714">Log level</span></span>

<span data-ttu-id="1ebd1-715">Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="1ebd1-716">O nível de log indica a gravidade ou importância.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="1ebd1-717">Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="1ebd1-718">O código a seguir cria os logs `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1ebd1-719">No código anterior, os `MyLogEvents.GetItem` parâmetros e `MyLogEvents.GetItemNotFound` são a [ID do evento de log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="1ebd1-720">O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1ebd1-721">Os parâmetros do método são explicados na [seção modelo de mensagem de log](#lmt) neste artigo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="1ebd1-722">Os métodos de log que incluem o nível no nome do método (por exemplo, `LogInformation` e `LogWarning`) são [métodos de extensão para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="1ebd1-723">Esses métodos chamam um método `Log` que recebe um parâmetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="1ebd1-724">Você pode chamar o método `Log` diretamente em vez de um desses métodos de extensão, mas a sintaxe é relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="1ebd1-725">Para saber mais, veja <xref:Microsoft.Extensions.Logging.ILogger> e o [código-fonte de extensões de agente](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="1ebd1-726">O ASP.NET Core define os seguintes níveis de log, ordenados aqui da menor para a maior gravidade.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="1ebd1-727">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="1ebd1-727">Trace = 0</span></span>

  <span data-ttu-id="1ebd1-728">Para obter informações que normalmente são valiosas somente para depuração.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="1ebd1-729">Essas mensagens podem conter dados confidenciais de aplicativos e, portanto, não devem ser habilitadas em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="1ebd1-730">*Desabilitado por padrão.*</span><span class="sxs-lookup"><span data-stu-id="1ebd1-730">*Disabled by default.*</span></span>

* <span data-ttu-id="1ebd1-731">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="1ebd1-731">Debug = 1</span></span>

  <span data-ttu-id="1ebd1-732">Para obter informações que possam ser úteis durante o desenvolvimento e a depuração.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="1ebd1-733">Exemplo: `Entering method Configure with flag set to true.` habilite logs de nível `Debug` em produção somente ao solucionar problemas, devido ao alto volume de logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="1ebd1-734">Information = 2</span><span class="sxs-lookup"><span data-stu-id="1ebd1-734">Information = 2</span></span>

  <span data-ttu-id="1ebd1-735">Para rastrear o fluxo geral do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="1ebd1-736">Esses logs normalmente têm algum valor a longo prazo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="1ebd1-737">Exemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="1ebd1-738">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="1ebd1-738">Warning = 3</span></span>

  <span data-ttu-id="1ebd1-739">Para eventos anormais ou inesperados no fluxo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="1ebd1-740">Eles podem incluir erros ou outras condições que não fazem com que o aplicativo pare, mas que talvez precisem ser investigados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="1ebd1-741">Exceções manipuladas são um local comum para usar o nível de log `Warning`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="1ebd1-742">Exemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="1ebd1-743">Error = 4</span><span class="sxs-lookup"><span data-stu-id="1ebd1-743">Error = 4</span></span>

  <span data-ttu-id="1ebd1-744">Para erros e exceções que não podem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1ebd1-745">Essas mensagens indicam uma falha na atividade ou na operação atual (como a solicitação HTTP atual) e não uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="1ebd1-746">Mensagem de log de exemplo:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="1ebd1-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="1ebd1-747">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="1ebd1-747">Critical = 5</span></span>

  <span data-ttu-id="1ebd1-748">Para falhas que exigem atenção imediata.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-748">For failures that require immediate attention.</span></span> <span data-ttu-id="1ebd1-749">Exemplos: cenários de perda de dados, espaço em disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="1ebd1-750">Use o nível de log para controlar a quantidade de saída de log que é gravada em uma mídia de armazenamento específica ou em uma janela de exibição.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="1ebd1-751">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-751">For example:</span></span>

* <span data-ttu-id="1ebd1-752">Em produção:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-752">In production:</span></span>
  * <span data-ttu-id="1ebd1-753">O registro em log no `Trace` por meio `Information` de níveis produz um alto volume de mensagens de log detalhadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1ebd1-754">Para controlar os custos e não exceder os limites de armazenamento de dados, faça logon `Trace` por meio de `Information` mensagens de nível em um armazenamento de dados de alto volume e baixo custo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="1ebd1-755">O logon `Warning` por meio de `Critical` níveis geralmente produz menos mensagens de log menores.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="1ebd1-756">Portanto, os custos e os limites de armazenamento geralmente não são uma preocupação, o que resulta em maior flexibilidade de escolha de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="1ebd1-757">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-757">During development:</span></span>
  * <span data-ttu-id="1ebd1-758">Faça logon `Warning` pelas `Critical` mensagens no console.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="1ebd1-759">Adicione `Trace` `Information` mensagens ao solucionar o problema.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="1ebd1-760">A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="1ebd1-761">O ASP.NET Core grava logs para eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1ebd1-762">Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="1ebd1-763">Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="1ebd1-764">ID de evento de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-764">Log event ID</span></span>

<span data-ttu-id="1ebd1-765">Cada log pode especificar uma *ID do evento*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1ebd1-766">O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="1ebd1-767">Uma ID de evento associa um conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-767">An event ID associates a set of events.</span></span> <span data-ttu-id="1ebd1-768">Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1ebd1-769">O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1ebd1-770">O provedor de Depuração não mostra IDs de eventos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1ebd1-771">O provedor de console mostra IDs de evento entre colchetes após a categoria:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="1ebd1-772">Modelo de mensagem de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-772">Log message template</span></span>

<span data-ttu-id="1ebd1-773">Cada log especifica um modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-773">Each log specifies a message template.</span></span> <span data-ttu-id="1ebd1-774">O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1ebd1-775">Use nomes para os espaços reservados, não números.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1ebd1-776">A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1ebd1-777">No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1ebd1-778">Esse código cria uma mensagem de log com os valores de parâmetro na sequência:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="1ebd1-779">A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="1ebd1-780">Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1ebd1-781">Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1ebd1-782">Por exemplo, suponha que as chamadas de método do agente sejam assim:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1ebd1-783">Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="1ebd1-784">Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="1ebd1-785">Exceções de registro em log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-785">Logging exceptions</span></span>

<span data-ttu-id="1ebd1-786">Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="1ebd1-787">Provedores diferentes manipulam as informações de exceção de maneiras diferentes.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="1ebd1-788">Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="1ebd1-789">Filtragem de linha</span><span class="sxs-lookup"><span data-stu-id="1ebd1-789">Log filtering</span></span>

<span data-ttu-id="1ebd1-790">Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="1ebd1-791">Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="1ebd1-792">Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="1ebd1-793">O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="1ebd1-794">Criar regras de filtro na configuração</span><span class="sxs-lookup"><span data-stu-id="1ebd1-794">Create filter rules in configuration</span></span>

<span data-ttu-id="1ebd1-795">O código de modelo de projeto chama `CreateDefaultBuilder` para configurar o registro em log para os provedores console, Debug e EventSource (ASP.NET Core 2,2 ou posteriores).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="1ebd1-796">O método `CreateDefaultBuilder` configura o registro em log para procurar a configuração em uma seção `Logging`, conforme explicado [anteriormente neste artigo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="1ebd1-797">Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="1ebd1-798">Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="1ebd1-799">Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="1ebd1-800">Regras de filtro no código</span><span class="sxs-lookup"><span data-stu-id="1ebd1-800">Filter rules in code</span></span>

<span data-ttu-id="1ebd1-801">O exemplo a seguir mostra como registrar regras de filtro no código:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="1ebd1-802">O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="1ebd1-803">O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1ebd1-804">Como as regras de filtragem são aplicadas</span><span class="sxs-lookup"><span data-stu-id="1ebd1-804">How filtering rules are applied</span></span>

<span data-ttu-id="1ebd1-805">Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="1ebd1-806">As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="1ebd1-807">Número</span><span class="sxs-lookup"><span data-stu-id="1ebd1-807">Number</span></span> | <span data-ttu-id="1ebd1-808">Provedor</span><span class="sxs-lookup"><span data-stu-id="1ebd1-808">Provider</span></span>      | <span data-ttu-id="1ebd1-809">Categorias que começam com...</span><span class="sxs-lookup"><span data-stu-id="1ebd1-809">Categories that begin with ...</span></span>          | <span data-ttu-id="1ebd1-810">Nível de log mínimo</span><span class="sxs-lookup"><span data-stu-id="1ebd1-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="1ebd1-811">1</span><span class="sxs-lookup"><span data-stu-id="1ebd1-811">1</span></span>      | <span data-ttu-id="1ebd1-812">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-812">Debug</span></span>         | <span data-ttu-id="1ebd1-813">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="1ebd1-813">All categories</span></span>                          | <span data-ttu-id="1ebd1-814">Informações</span><span class="sxs-lookup"><span data-stu-id="1ebd1-814">Information</span></span>       |
| <span data-ttu-id="1ebd1-815">2</span><span class="sxs-lookup"><span data-stu-id="1ebd1-815">2</span></span>      | <span data-ttu-id="1ebd1-816">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-816">Console</span></span>       | <span data-ttu-id="1ebd1-817">Microsoft. AspNetCore. Mvc. Razor . Interno</span><span class="sxs-lookup"><span data-stu-id="1ebd1-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="1ebd1-818">Aviso</span><span class="sxs-lookup"><span data-stu-id="1ebd1-818">Warning</span></span>           |
| <span data-ttu-id="1ebd1-819">3</span><span class="sxs-lookup"><span data-stu-id="1ebd1-819">3</span></span>      | <span data-ttu-id="1ebd1-820">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-820">Console</span></span>       | <span data-ttu-id="1ebd1-821">Microsoft. AspNetCore. Mvc. Razor .Razor</span><span class="sxs-lookup"><span data-stu-id="1ebd1-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="1ebd1-822">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-822">Debug</span></span>             |
| <span data-ttu-id="1ebd1-823">4</span><span class="sxs-lookup"><span data-stu-id="1ebd1-823">4</span></span>      | <span data-ttu-id="1ebd1-824">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-824">Console</span></span>       | <span data-ttu-id="1ebd1-825">Microsoft. AspNetCore. Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="1ebd1-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="1ebd1-826">Erro</span><span class="sxs-lookup"><span data-stu-id="1ebd1-826">Error</span></span>             |
| <span data-ttu-id="1ebd1-827">5</span><span class="sxs-lookup"><span data-stu-id="1ebd1-827">5</span></span>      | <span data-ttu-id="1ebd1-828">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-828">Console</span></span>       | <span data-ttu-id="1ebd1-829">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="1ebd1-829">All categories</span></span>                          | <span data-ttu-id="1ebd1-830">Informações</span><span class="sxs-lookup"><span data-stu-id="1ebd1-830">Information</span></span>       |
| <span data-ttu-id="1ebd1-831">6</span><span class="sxs-lookup"><span data-stu-id="1ebd1-831">6</span></span>      | <span data-ttu-id="1ebd1-832">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="1ebd1-832">All providers</span></span> | <span data-ttu-id="1ebd1-833">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="1ebd1-833">All categories</span></span>                          | <span data-ttu-id="1ebd1-834">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-834">Debug</span></span>             |
| <span data-ttu-id="1ebd1-835">7</span><span class="sxs-lookup"><span data-stu-id="1ebd1-835">7</span></span>      | <span data-ttu-id="1ebd1-836">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="1ebd1-836">All providers</span></span> | <span data-ttu-id="1ebd1-837">Sistema</span><span class="sxs-lookup"><span data-stu-id="1ebd1-837">System</span></span>                                  | <span data-ttu-id="1ebd1-838">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-838">Debug</span></span>             |
| <span data-ttu-id="1ebd1-839">8</span><span class="sxs-lookup"><span data-stu-id="1ebd1-839">8</span></span>      | <span data-ttu-id="1ebd1-840">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-840">Debug</span></span>         | <span data-ttu-id="1ebd1-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="1ebd1-841">Microsoft</span></span>                               | <span data-ttu-id="1ebd1-842">Trace</span><span class="sxs-lookup"><span data-stu-id="1ebd1-842">Trace</span></span>             |

<span data-ttu-id="1ebd1-843">Quando um objeto `ILogger` é criado, o objeto `ILoggerFactory` seleciona uma única regra por provedor para aplicar a esse agente.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1ebd1-844">Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1ebd1-845">A regra mais específica possível para cada par de categoria e provedor é selecionada dentre as regras disponíveis.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1ebd1-846">O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1ebd1-847">Selecione todas as regras que correspondem ao provedor ou seu alias.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1ebd1-848">Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1ebd1-849">Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1ebd1-850">Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1ebd1-851">Se várias regras forem selecionadas, use a **última**.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1ebd1-852">Se nenhuma regra for selecionada, use `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="1ebd1-853">Com a lista anterior de regras, suponha que você crie um `ILogger` objeto para a categoria "Microsoft. AspNetCore. Mvc. Razor . Razor ViewEngine":</span><span class="sxs-lookup"><span data-stu-id="1ebd1-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="1ebd1-854">Para o provedor Depuração as regras 1, 6 e 8 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="1ebd1-855">A regra 8 é mais específica, portanto é a que será selecionada.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="1ebd1-856">Para o provedor Console as regras 3, 4, 5 e 6 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="1ebd1-857">A regra 3 é a mais específica.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="1ebd1-858">A instância `ILogger` resultante envia logs de nível `Trace` e superior para o provedor Depuração.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="1ebd1-859">Logs de nível `Debug` e superior são enviados para o provedor Console.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="1ebd1-860">Aliases de provedor</span><span class="sxs-lookup"><span data-stu-id="1ebd1-860">Provider aliases</span></span>

<span data-ttu-id="1ebd1-861">Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="1ebd1-862">Para os provedores internos, use os seguintes aliases:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="1ebd1-863">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-863">Console</span></span>
* <span data-ttu-id="1ebd1-864">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-864">Debug</span></span>
* <span data-ttu-id="1ebd1-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-865">EventSource</span></span>
* <span data-ttu-id="1ebd1-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ebd1-866">EventLog</span></span>
* <span data-ttu-id="1ebd1-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-867">TraceSource</span></span>
* <span data-ttu-id="1ebd1-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1ebd1-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="1ebd1-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ebd1-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="1ebd1-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="1ebd1-871">Nível mínimo padrão</span><span class="sxs-lookup"><span data-stu-id="1ebd1-871">Default minimum level</span></span>

<span data-ttu-id="1ebd1-872">Há uma configuração de nível mínimo que entra em vigor somente se nenhuma regra de código ou de configuração se aplicar a um provedor e uma categoria determinados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="1ebd1-873">O exemplo a seguir mostra como definir o nível mínimo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="1ebd1-874">Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="1ebd1-875">Funções de filtro</span><span class="sxs-lookup"><span data-stu-id="1ebd1-875">Filter functions</span></span>

<span data-ttu-id="1ebd1-876">Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="1ebd1-877">O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="1ebd1-878">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="1ebd1-879">Categorias e níveis de sistema</span><span class="sxs-lookup"><span data-stu-id="1ebd1-879">System categories and levels</span></span>

<span data-ttu-id="1ebd1-880">Veja algumas categorias usadas pelo ASP.NET Core e Entity Framework Core, com anotações sobre quais logs esperar delas:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="1ebd1-881">Categoria</span><span class="sxs-lookup"><span data-stu-id="1ebd1-881">Category</span></span>                            | <span data-ttu-id="1ebd1-882">Observações</span><span class="sxs-lookup"><span data-stu-id="1ebd1-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1ebd1-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1ebd1-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1ebd1-884">Diagnóstico geral de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1ebd1-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1ebd1-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1ebd1-886">Quais chaves foram consideradas, encontradas e usadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1ebd1-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1ebd1-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1ebd1-888">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-888">Hosts allowed.</span></span> |
| <span data-ttu-id="1ebd1-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1ebd1-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1ebd1-890">Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1ebd1-891">Quais assemblies de inicialização de hospedagem foram carregados.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1ebd1-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1ebd1-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1ebd1-893">MVC e Razor diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1ebd1-894">Model binding, execução de filtro, compilação de exibição, seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1ebd1-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1ebd1-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1ebd1-896">Informações de correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-896">Route matching information.</span></span> |
| <span data-ttu-id="1ebd1-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1ebd1-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1ebd1-898">Respostas de início, parada e atividade da conexão.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1ebd1-899">Informações sobre o certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1ebd1-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1ebd1-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1ebd1-901">Arquivos atendidos.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-901">Files served.</span></span> |
| <span data-ttu-id="1ebd1-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1ebd1-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1ebd1-903">Diagnóstico geral do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1ebd1-904">Atividade e configuração do banco de dados, detecção de alterações, migrações.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="1ebd1-905">Escopos de log</span><span class="sxs-lookup"><span data-stu-id="1ebd1-905">Log scopes</span></span>

 <span data-ttu-id="1ebd1-906">Um *escopo* pode agrupar um conjunto de operações lógicas.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1ebd1-907">Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1ebd1-908">Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1ebd1-909">Um escopo é um tipo `IDisposable` retornado pelo método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e que dura até que seja descartado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="1ebd1-910">Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="1ebd1-911">O código a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="1ebd1-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="1ebd1-913">A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="1ebd1-914">Saiba mais sobre como configurar na seção [Configuração](#configuration).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="1ebd1-915">Cada mensagem de log inclui as informações com escopo definido:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="1ebd1-916">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-916">Built-in logging providers</span></span>

<span data-ttu-id="1ebd1-917">O ASP.NET Core vem com os seguintes provedores:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="1ebd1-918">Console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="1ebd1-919">Depurar</span><span class="sxs-lookup"><span data-stu-id="1ebd1-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="1ebd1-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="1ebd1-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ebd1-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="1ebd1-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="1ebd1-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1ebd1-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1ebd1-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ebd1-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1ebd1-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="1ebd1-926">Para obter informações sobre StdOut e o log de depuração com o Módulo do ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="1ebd1-927">Provedor do console</span><span class="sxs-lookup"><span data-stu-id="1ebd1-927">Console provider</span></span>

<span data-ttu-id="1ebd1-928">O pacote de provedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envia a saída de log para o console.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="1ebd1-929">Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="1ebd1-930">Depurar provedor</span><span class="sxs-lookup"><span data-stu-id="1ebd1-930">Debug provider</span></span>

<span data-ttu-id="1ebd1-931">O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="1ebd1-932">No Linux, esse provedor grava logs em */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="1ebd1-933">Provedor de origem do evento</span><span class="sxs-lookup"><span data-stu-id="1ebd1-933">Event Source provider</span></span>

<span data-ttu-id="1ebd1-934">O pacote do provedor [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) grava em uma origem de evento entre plataformas com o nome `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="1ebd1-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1ebd1-935">No Windows, o provedor usa o [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="1ebd1-936">O provedor de origem do evento é adicionado automaticamente quando `CreateDefaultBuilder` é chamado para compilar o host.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="1ebd1-937">Use o [utilitário Perfview](https://github.com/Microsoft/perfview) para coletar e exibir logs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1ebd1-938">Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1ebd1-939">Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1ebd1-940">(Não se esqueça do asterisco no início da cadeia de caracteres).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="1ebd1-942">Provedor EventLog do Windows</span><span class="sxs-lookup"><span data-stu-id="1ebd1-942">Windows EventLog provider</span></span>

<span data-ttu-id="1ebd1-943">O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="1ebd1-944">As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1ebd1-945">Se `null` ou não for especificado, as seguintes configurações padrão serão usadas:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1ebd1-946">`LogName`: "Aplicativo"</span><span class="sxs-lookup"><span data-stu-id="1ebd1-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="1ebd1-947">`SourceName`: "Tempo de execução do .NET"</span><span class="sxs-lookup"><span data-stu-id="1ebd1-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="1ebd1-948">`MachineName`: O nome do computador local é usado.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="1ebd1-949">Os eventos são registrados em log para o [nível de aviso e superior](#log-level).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="1ebd1-950">O exemplo a seguir define o nível de log padrão do log de eventos como <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="1ebd1-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="1ebd1-951">Provedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="1ebd1-951">TraceSource provider</span></span>

<span data-ttu-id="1ebd1-952">O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="1ebd1-953">As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="1ebd1-954">Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="1ebd1-955">O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="1ebd1-956">Provedor do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="1ebd1-956">Azure App Service provider</span></span>

<span data-ttu-id="1ebd1-957">O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="1ebd1-958">O pacote de provedor não está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="1ebd1-959">Se você estiver direcionando para o .NET Framework ou referenciando o metapacote `Microsoft.AspNetCore.App`, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="1ebd1-960">Uma sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite passar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="1ebd1-961">O objeto settings pode substituir as configurações padrão, como o modelo de saída de registro em log, o nome do blob e o limite do tamanho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="1ebd1-962">(O *modelo Output* é um modelo de mensagem aplicado a todos os logs, além daquele fornecido com uma chamada ao método `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="1ebd1-963">Quando você implanta em um aplicativo do Serviço de Aplicativo, o aplicativo respeita as configurações na seção [Logs do Serviço de Aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) da página **Serviço de Aplicativo** do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1ebd1-964">Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1ebd1-965">**Log de aplicativo (Sistema de Arquivos)**</span><span class="sxs-lookup"><span data-stu-id="1ebd1-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1ebd1-966">**Log de aplicativo (Blob)**</span><span class="sxs-lookup"><span data-stu-id="1ebd1-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1ebd1-967">O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1ebd1-968">O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1ebd1-969">O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1ebd1-970">O provedor funciona somente quando o projeto é executado no ambiente do Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="1ebd1-971">Ele não tem nenhum efeito quando o projeto é executado localmente&mdash;ele não grava em arquivos locais ou no armazenamento de desenvolvimento local para blobs.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1ebd1-972">Fluxo de log do Azure</span><span class="sxs-lookup"><span data-stu-id="1ebd1-972">Azure log streaming</span></span>

<span data-ttu-id="1ebd1-973">O fluxo de log do Azure permite que você exiba a atividade de log em tempo real:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="1ebd1-974">O servidor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="1ebd1-974">The app server</span></span>
* <span data-ttu-id="1ebd1-975">Do servidor Web</span><span class="sxs-lookup"><span data-stu-id="1ebd1-975">The web server</span></span>
* <span data-ttu-id="1ebd1-976">De uma solicitação de rastreio com falha</span><span class="sxs-lookup"><span data-stu-id="1ebd1-976">Failed request tracing</span></span>

<span data-ttu-id="1ebd1-977">Para configurar o fluxo de log do Azure:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1ebd1-978">Navegue até a página **Logs do Serviço de Aplicativo** da página do portal do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="1ebd1-979">Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1ebd1-980">Escolha o **Nível** de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-980">Choose the log **Level**.</span></span> <span data-ttu-id="1ebd1-981">Essa configuração se aplica somente ao streaming de log do Azure, não a outros provedores de log no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="1ebd1-982">Navegue até a página **Fluxo de Log** para exibir as mensagens de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="1ebd1-983">Elas são registradas pelo aplicativo por meio da interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="1ebd1-984">Log de rastreamento do Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="1ebd1-985">O pacote de provedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava os logs no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="1ebd1-986">O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1ebd1-987">Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1ebd1-988">O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1ebd1-989">Se você usar esse pacote, não precisará instalar o pacote de provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1ebd1-990">Não use o pacote [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que é para o ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="1ebd1-991">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1ebd1-992">Visão geral do Application Insights</span><span class="sxs-lookup"><span data-stu-id="1ebd1-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1ebd1-993">[Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1ebd1-994">[ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1ebd1-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs) (Adaptadores de registro em log do Application Insights).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1ebd1-996">[Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1ebd1-997">Provedores de log de terceiros</span><span class="sxs-lookup"><span data-stu-id="1ebd1-997">Third-party logging providers</span></span>

<span data-ttu-id="1ebd1-998">Estruturas de log de terceiros que funcionam com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1ebd1-999">[elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1ebd1-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1ebd1-1001">[JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1ebd1-1002">[KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1ebd1-1003">[Log4net](https://logging.apache.org/log4net/) ([repositório GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1ebd1-1004">[Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1ebd1-1005">[NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1ebd1-1006">[Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1ebd1-1007">[Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1ebd1-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1ebd1-1009">Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1ebd1-1010">Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1ebd1-1011">Adicione um pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1ebd1-1012">Chame um `ILoggerFactory` método de extensão fornecido pela estrutura de log.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1ebd1-1013">Para saber mais, consulte a documentação de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1ebd1-1014">Não há suporte para provedores de log de terceiros na Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ebd1-1015">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1ebd1-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
