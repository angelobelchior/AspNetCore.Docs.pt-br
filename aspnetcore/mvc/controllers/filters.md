---
title: Filtros no ASP.NET Core
author: Rick-Anderson
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: d075faa951a34fb3856b54eb9e21593b6616b4f1
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673959"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="af73c-103">Filtros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af73c-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af73c-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="af73c-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="af73c-105">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="af73c-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="af73c-106">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="af73c-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="af73c-107">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="af73c-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="af73c-108">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="af73c-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="af73c-109">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="af73c-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="af73c-110">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="af73c-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="af73c-111">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="af73c-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="af73c-112">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="af73c-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="af73c-113">Este documento se aplica a Razor páginas, controladores de API e controladores com exibições.</span><span class="sxs-lookup"><span data-stu-id="af73c-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="af73c-114">Os filtros não funcionam diretamente com os [ Razor componentes](xref:blazor/components/index)do.</span><span class="sxs-lookup"><span data-stu-id="af73c-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="af73c-115">Um filtro só pode afetar indiretamente um componente quando:</span><span class="sxs-lookup"><span data-stu-id="af73c-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="af73c-116">O componente é inserido em uma página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="af73c-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="af73c-117">A página ou o controlador/modo de exibição usa o filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="af73c-118">[Exibir ou baixar exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="af73c-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="af73c-119">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="af73c-119">How filters work</span></span>

<span data-ttu-id="af73c-120">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="af73c-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="af73c-121">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="af73c-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de outro middleware, middleware de roteamento, seleção de ação e o pipeline de invocação de ação.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="af73c-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-124">Filter types</span></span>

<span data-ttu-id="af73c-125">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="af73c-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="af73c-126">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="af73c-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="af73c-127">Os filtros de autorização de circuito curto do pipeline se a solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="af73c-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="af73c-128">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="af73c-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="af73c-129">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="af73c-129">Run after authorization.</span></span>  
  * <span data-ttu-id="af73c-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> executa o código antes do resto do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="af73c-131">Por exemplo, `OnResourceExecuting` executa o código antes da Associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="af73c-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="af73c-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> executa o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="af73c-133">[Filtros de ação](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="af73c-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="af73c-134">Execute o código imediatamente antes e depois que um método de ação for chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="af73c-135">Pode alterar os argumentos passados para uma ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="af73c-136">Pode alterar o resultado retornado da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="af73c-137">**Não** têm suporte em Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="af73c-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="af73c-138">Os [filtros de exceção](#exception-filters) aplicam políticas globais a exceções sem tratamento que ocorrem antes da gravação do corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="af73c-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="af73c-139">Os [filtros de resultado](#result-filters) executam o código imediatamente antes e após a execução dos resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="af73c-140">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="af73c-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="af73c-141">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="af73c-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="af73c-142">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="af73c-145">Implementação</span><span class="sxs-lookup"><span data-stu-id="af73c-145">Implementation</span></span>

<span data-ttu-id="af73c-146">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="af73c-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="af73c-147">Os filtros síncronos executam o código antes e depois do estágio do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="af73c-148">Por exemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="af73c-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="af73c-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="af73c-150">No código anterior, [mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) é uma função de utilitário no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="af73c-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="af73c-151">Os filtros assíncronos definem um `On-Stage-ExecutionAsync` método.</span><span class="sxs-lookup"><span data-stu-id="af73c-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="af73c-152">Por exemplo <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="af73c-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="af73c-153">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="af73c-154">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-154">Multiple filter stages</span></span>

<span data-ttu-id="af73c-155">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="af73c-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="af73c-156">Por exemplo, a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implementa:</span><span class="sxs-lookup"><span data-stu-id="af73c-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="af73c-157">Síncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="af73c-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="af73c-158">Assíncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="af73c-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="af73c-159">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="af73c-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="af73c-160">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="af73c-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="af73c-161">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="af73c-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="af73c-162">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="af73c-163">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , substitua apenas os métodos síncronos ou os métodos assíncronos para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="af73c-164">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="af73c-164">Built-in filter attributes</span></span>

<span data-ttu-id="af73c-165">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="af73c-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="af73c-166">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="af73c-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="af73c-167">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="af73c-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="af73c-168">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="af73c-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="af73c-169">Use uma ferramenta como as [ferramentas de desenvolvedor do navegador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="af73c-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="af73c-170">Em **cabeçalhos de resposta**, `author: Rick Anderson` é exibido.</span><span class="sxs-lookup"><span data-stu-id="af73c-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="af73c-171">O código a seguir implementa um `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="af73c-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="af73c-172">Lê o título e o nome do sistema de configuração.</span><span class="sxs-lookup"><span data-stu-id="af73c-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="af73c-173">Ao contrário do exemplo anterior, o código a seguir não exige que os parâmetros de filtro sejam adicionados ao código.</span><span class="sxs-lookup"><span data-stu-id="af73c-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="af73c-174">Adiciona o título e o nome ao cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="af73c-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="af73c-175">As opções de configuração são fornecidas no [sistema de configuração](xref:fundamentals/configuration/index) usando o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="af73c-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="af73c-176">Por exemplo, no *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="af73c-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="af73c-177">No `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="af73c-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="af73c-178">A `PositionOptions` classe é adicionada ao contêiner de serviço com a `"Position"` área de configuração.</span><span class="sxs-lookup"><span data-stu-id="af73c-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="af73c-179">O `MyActionFilterAttribute` é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="af73c-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="af73c-180">O código a seguir mostra a `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="af73c-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="af73c-181">O código a seguir aplica o `MyActionFilterAttribute` ao `Index2` método:</span><span class="sxs-lookup"><span data-stu-id="af73c-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="af73c-182">Em **cabeçalhos de resposta**, `author: Rick Anderson` e `Editor: Joe Smith` é exibido quando o `Sample/Index2` ponto de extremidade é chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="af73c-183">O código a seguir aplica o `MyActionFilterAttribute` e o `AddHeaderAttribute` à Razor página:</span><span class="sxs-lookup"><span data-stu-id="af73c-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="af73c-184">Os filtros não podem ser aplicados aos Razor métodos do manipulador de páginas.</span><span class="sxs-lookup"><span data-stu-id="af73c-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="af73c-185">Eles podem ser aplicados ao modelo de Razor página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="af73c-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="af73c-186">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="af73c-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="af73c-187">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="af73c-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="af73c-188">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="af73c-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="af73c-189">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="af73c-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="af73c-190">Usando um atributo em uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="af73c-191">Atributos de filtro não podem ser aplicados a Razor métodos de manipulador de páginas.</span><span class="sxs-lookup"><span data-stu-id="af73c-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="af73c-192">Usando um atributo em um controlador ou uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="af73c-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="af73c-193">Globalmente para todos os controladores, ações e Razor páginas, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="af73c-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="af73c-194">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="af73c-194">Default order of execution</span></span>

<span data-ttu-id="af73c-195">Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="af73c-196">Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método.</span><span class="sxs-lookup"><span data-stu-id="af73c-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="af73c-197">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="af73c-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="af73c-198">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="af73c-198">The filter sequence:</span></span>

* <span data-ttu-id="af73c-199">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="af73c-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="af73c-200">O código *anterior* de filtros de Razor página e controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="af73c-201">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="af73c-202">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="af73c-203">O código *After* de filtros de Razor página e controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="af73c-204">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="af73c-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="af73c-205">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="af73c-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="af73c-206">Sequência</span><span class="sxs-lookup"><span data-stu-id="af73c-206">Sequence</span></span> | <span data-ttu-id="af73c-207">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-207">Filter scope</span></span> | <span data-ttu-id="af73c-208">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="af73c-209">1</span><span class="sxs-lookup"><span data-stu-id="af73c-209">1</span></span> | <span data-ttu-id="af73c-210">Global</span><span class="sxs-lookup"><span data-stu-id="af73c-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="af73c-211">2</span><span class="sxs-lookup"><span data-stu-id="af73c-211">2</span></span> | <span data-ttu-id="af73c-212">Controlador ou Razor página</span><span class="sxs-lookup"><span data-stu-id="af73c-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="af73c-213">3</span><span class="sxs-lookup"><span data-stu-id="af73c-213">3</span></span> | <span data-ttu-id="af73c-214">Método</span><span class="sxs-lookup"><span data-stu-id="af73c-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="af73c-215">4</span><span class="sxs-lookup"><span data-stu-id="af73c-215">4</span></span> | <span data-ttu-id="af73c-216">Método</span><span class="sxs-lookup"><span data-stu-id="af73c-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="af73c-217">5</span><span class="sxs-lookup"><span data-stu-id="af73c-217">5</span></span> | <span data-ttu-id="af73c-218">Controlador ou Razor página</span><span class="sxs-lookup"><span data-stu-id="af73c-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="af73c-219">6</span><span class="sxs-lookup"><span data-stu-id="af73c-219">6</span></span> | <span data-ttu-id="af73c-220">Global</span><span class="sxs-lookup"><span data-stu-id="af73c-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="af73c-221">Filtros de nível de controlador</span><span class="sxs-lookup"><span data-stu-id="af73c-221">Controller level filters</span></span>

<span data-ttu-id="af73c-222">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="af73c-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="af73c-223">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="af73c-223">These methods:</span></span>

* <span data-ttu-id="af73c-224">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="af73c-225">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="af73c-226">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="af73c-227">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="af73c-228">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="af73c-229">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="af73c-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="af73c-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="af73c-230">The `TestController`:</span></span>

* <span data-ttu-id="af73c-231">Aplica o `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à `FilterTest2` ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="af73c-232">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="af73c-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="af73c-233">Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="af73c-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="af73c-234">Filtros de nível de controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="af73c-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="af73c-235">Os filtros de nível de controlador **não** podem ser definidos para execução após os filtros aplicados aos métodos.</span><span class="sxs-lookup"><span data-stu-id="af73c-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="af73c-236">A ordem é explicada na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="af73c-236">Order is explained in the next section.</span></span>

<span data-ttu-id="af73c-237">Para Razor páginas, consulte [implementar Razor filtros de página substituindo métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="af73c-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="af73c-238">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="af73c-238">Overriding the default order</span></span>

<span data-ttu-id="af73c-239">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="af73c-240">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="af73c-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="af73c-241">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="af73c-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="af73c-242">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="af73c-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="af73c-243">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="af73c-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="af73c-244">A `Order` propriedade é definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="af73c-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="af73c-245">Considere os dois filtros de ação no controlador a seguir:</span><span class="sxs-lookup"><span data-stu-id="af73c-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="af73c-246">Um filtro global é adicionado em `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af73c-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="af73c-247">Os 3 filtros são executados na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="af73c-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="af73c-248">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="af73c-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="af73c-249">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="af73c-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="af73c-250">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="af73c-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="af73c-251">Conforme mencionado anteriormente, os filtros no nível do controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` para filtros internos, o escopo determina a ordem, a menos que `Order` esteja definido como um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="af73c-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="af73c-252">No código anterior, `MySampleActionFilter` tem escopo global para que ele seja executado antes `MyAction2FilterAttribute` , que tem o escopo do controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="af73c-253">Para fazer a `MyAction2FilterAttribute` execução primeiro, defina a ordem como `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="af73c-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="af73c-254">Para fazer o filtro global `MySampleActionFilter` ser executado primeiro, defina `Order` como `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="af73c-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="af73c-255">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="af73c-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="af73c-256">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="af73c-257">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="af73c-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="af73c-258">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="af73c-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="af73c-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="af73c-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="af73c-260">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="af73c-261">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="af73c-262">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="af73c-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="af73c-263">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="af73c-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="af73c-264">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="af73c-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="af73c-265">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="af73c-265">Dependency injection</span></span>

<span data-ttu-id="af73c-266">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="af73c-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="af73c-267">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="af73c-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="af73c-268">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="af73c-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="af73c-269">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="af73c-269">A type-activated filter means:</span></span>

* <span data-ttu-id="af73c-270">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="af73c-270">An instance is created for each request.</span></span>
* <span data-ttu-id="af73c-271">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="af73c-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="af73c-272">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="af73c-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="af73c-273">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="af73c-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="af73c-274">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="af73c-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="af73c-275">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="af73c-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="af73c-276">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="af73c-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="af73c-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="af73c-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="af73c-278">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="af73c-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="af73c-279">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-279">Loggers are available from DI.</span></span> <span data-ttu-id="af73c-280">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="af73c-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="af73c-281">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="af73c-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="af73c-282">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="af73c-282">Logging added to filters:</span></span>

* <span data-ttu-id="af73c-283">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="af73c-284">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="af73c-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="af73c-285">Os filtros internos registram ações de log e eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="af73c-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="af73c-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="af73c-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="af73c-287">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="af73c-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="af73c-288">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="af73c-289">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="af73c-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="af73c-290">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="af73c-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="af73c-291">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="af73c-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="af73c-292">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="af73c-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="af73c-293">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="af73c-294">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="af73c-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="af73c-295">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="af73c-296">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="af73c-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="af73c-297">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="af73c-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="af73c-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="af73c-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="af73c-299">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="af73c-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="af73c-300">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="af73c-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="af73c-301">TypeFilterAttribute</span></span>

<span data-ttu-id="af73c-302">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="af73c-303">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="af73c-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="af73c-304">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="af73c-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="af73c-305">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="af73c-306">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="af73c-307">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="af73c-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="af73c-308">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="af73c-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="af73c-309">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="af73c-310">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="af73c-311">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="af73c-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="af73c-312">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="af73c-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="af73c-313">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="af73c-313">Authorization filters</span></span>

<span data-ttu-id="af73c-314">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="af73c-314">Authorization filters:</span></span>

* <span data-ttu-id="af73c-315">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="af73c-316">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-316">Control access to action methods.</span></span>
* <span data-ttu-id="af73c-317">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="af73c-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="af73c-318">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="af73c-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="af73c-319">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="af73c-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="af73c-320">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="af73c-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="af73c-321">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="af73c-321">Calls the authorization system.</span></span>
* <span data-ttu-id="af73c-322">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="af73c-322">Does not authorize requests.</span></span>

<span data-ttu-id="af73c-323">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="af73c-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="af73c-324">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="af73c-324">The exception will not be handled.</span></span>
* <span data-ttu-id="af73c-325">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="af73c-326">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="af73c-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="af73c-327">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="af73c-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="af73c-328">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="af73c-328">Resource filters</span></span>

<span data-ttu-id="af73c-329">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="af73c-329">Resource filters:</span></span>

* <span data-ttu-id="af73c-330">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="af73c-331">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="af73c-332">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="af73c-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="af73c-333">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="af73c-334">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="af73c-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="af73c-335">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="af73c-335">Resource filter examples:</span></span>

* <span data-ttu-id="af73c-336">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="af73c-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="af73c-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="af73c-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="af73c-338">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="af73c-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="af73c-339">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="af73c-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="af73c-340">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="af73c-340">Action filters</span></span>

<span data-ttu-id="af73c-341">Os filtros de ação **não** se aplicam a Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="af73c-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="af73c-342">Razor Páginas dão suporte a <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="af73c-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="af73c-343">Para obter mais informações, consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="af73c-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="af73c-344">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-344">Action filters:</span></span>

* <span data-ttu-id="af73c-345">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="af73c-346">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="af73c-347">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="af73c-348">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="af73c-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="af73c-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – permite ler as entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="af73c-350"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="af73c-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="af73c-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="af73c-352">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="af73c-353">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="af73c-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="af73c-354">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="af73c-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="af73c-355">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="af73c-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="af73c-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="af73c-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="af73c-358">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="af73c-358">Setting this property to null:</span></span>

  * <span data-ttu-id="af73c-359">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="af73c-360">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="af73c-361">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="af73c-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="af73c-362">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="af73c-363">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="af73c-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="af73c-364">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="af73c-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="af73c-365">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="af73c-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="af73c-366">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="af73c-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="af73c-367">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="af73c-367">Validate model state.</span></span>
* <span data-ttu-id="af73c-368">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="af73c-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="af73c-369">Os controladores anotados com o `[ApiController]` atributo validam automaticamente o estado do modelo e retornam uma resposta de 400.</span><span class="sxs-lookup"><span data-stu-id="af73c-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="af73c-370">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="af73c-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="af73c-371">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="af73c-372">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="af73c-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="af73c-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="af73c-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="af73c-375">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="af73c-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="af73c-376">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="af73c-377">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="af73c-378">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="af73c-378">Exception filters</span></span>

<span data-ttu-id="af73c-379">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-379">Exception filters:</span></span>

* <span data-ttu-id="af73c-380">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="af73c-381">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="af73c-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="af73c-382">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="af73c-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="af73c-383">O código a seguir testa o filtro de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="af73c-384">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-384">Exception filters:</span></span>

* <span data-ttu-id="af73c-385">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="af73c-385">Don't have before and after events.</span></span>
* <span data-ttu-id="af73c-386">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="af73c-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="af73c-387">Manipule exceções sem tratamento que ocorrem em Razor criação de página ou controlador, [Associação de modelo](xref:mvc/models/model-binding), filtros de ação ou métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="af73c-388">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="af73c-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="af73c-389">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="af73c-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="af73c-390">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-390">This stops propagation of the exception.</span></span> <span data-ttu-id="af73c-391">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="af73c-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="af73c-392">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="af73c-392">Only an action filter can do that.</span></span>

<span data-ttu-id="af73c-393">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-393">Exception filters:</span></span>

* <span data-ttu-id="af73c-394">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="af73c-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="af73c-395">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="af73c-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="af73c-396">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="af73c-397">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="af73c-398">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="af73c-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="af73c-399">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="af73c-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="af73c-400">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="af73c-400">Result filters</span></span>

<span data-ttu-id="af73c-401">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="af73c-401">Result filters:</span></span>

* <span data-ttu-id="af73c-402">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="af73c-402">Implement an interface:</span></span>
  * <span data-ttu-id="af73c-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="af73c-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="af73c-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="af73c-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="af73c-405">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="af73c-406">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="af73c-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="af73c-407">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="af73c-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="af73c-408">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="af73c-409">Uma ação que retorna uma exibição inclui todo o processamento do Razor como parte da <xref:Microsoft.AspNetCore.Mvc.ViewResult> execução.</span><span class="sxs-lookup"><span data-stu-id="af73c-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="af73c-410">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="af73c-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="af73c-411">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="af73c-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="af73c-412">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="af73c-413">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="af73c-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="af73c-414">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="af73c-415">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="af73c-416">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="af73c-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="af73c-417">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="af73c-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="af73c-418">Gerando uma exceção em `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="af73c-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="af73c-419">Impede a execução do resultado da ação e dos filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="af73c-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="af73c-420">É tratado como uma falha em vez de um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="af73c-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="af73c-421">Quando o <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> método é executado, a resposta provavelmente já foi enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="af73c-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="af73c-422">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada.</span><span class="sxs-lookup"><span data-stu-id="af73c-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="af73c-423">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="af73c-424">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="af73c-425">Definir `Exception` como NULL manipula uma exceção e impede que a exceção seja gerada novamente mais tarde no pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="af73c-426">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="af73c-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="af73c-427">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="af73c-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="af73c-428">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="af73c-429">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="af73c-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="af73c-430">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="af73c-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="af73c-431">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="af73c-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="af73c-432">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="af73c-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="af73c-433">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="af73c-434">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="af73c-434">This includes action results produced by:</span></span>

* <span data-ttu-id="af73c-435">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="af73c-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="af73c-436">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-436">Exception filters.</span></span>

<span data-ttu-id="af73c-437">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="af73c-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="af73c-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="af73c-438">IFilterFactory</span></span>

<span data-ttu-id="af73c-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="af73c-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="af73c-440">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="af73c-441">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="af73c-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="af73c-442">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="af73c-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="af73c-443">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="af73c-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="af73c-444">`IFilterFactory.IsReusable`:</span><span class="sxs-lookup"><span data-stu-id="af73c-444">`IFilterFactory.IsReusable`:</span></span>

* <span data-ttu-id="af73c-445">É uma dica pela fábrica que a instância de filtro criada pela fábrica pode ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-445">Is a hint by the factory that the filter instance created by the factory may be reused outside of the request scope it was created within.</span></span>
* <span data-ttu-id="af73c-446">\***Não** deve ser usado com um filtro que dependa de serviços com um tempo de vida diferente do singleton.</span><span class="sxs-lookup"><span data-stu-id="af73c-446">Should \***not** _ be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="af73c-447">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="af73c-447">The ASP.NET Core runtime doesn't guarantee:</span></span>

<span data-ttu-id="af73c-448">_ Uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-448">_ That a single instance of the filter will be created.</span></span>
* <span data-ttu-id="af73c-449">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="af73c-449">The filter will not be re-requested from the DI container at some later point.</span></span>

[!WARNING]<span data-ttu-id="af73c-450"> Somente configure `IFilterFactory.IsReusable` para retornar `true` se a origem dos filtros não for ambígua, se os filtros forem sem estado e forem seguros para uso em várias solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-450"> Only configure `IFilterFactory.IsReusable` to return `true` if the source of the filters is unambiguous, the filters are stateless, and are safe to use across multiple HTTP requests.</span></span> <span data-ttu-id="af73c-451">Por exemplo, não retorne filtros de DI que são registrados como escopo ou transitório se `IFilterFactory.IsReusable` retorna `true`</span><span class="sxs-lookup"><span data-stu-id="af73c-451">For instance, do not return filters from DI that are registered as scoped or transient if `IFilterFactory.IsReusable` returns `true`</span></span>

<span data-ttu-id="af73c-452">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="af73c-452">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="af73c-453">O filtro é aplicado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="af73c-453">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="af73c-454">Teste o código anterior executando o [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="af73c-454">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="af73c-455">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="af73c-455">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="af73c-456">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="af73c-456">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="af73c-457">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="af73c-457">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="af73c-458">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="af73c-458">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="af73c-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="af73c-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="af73c-460">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="af73c-460">**internal:** `My header`</span></span>

<span data-ttu-id="af73c-461">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="af73c-461">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="af73c-462">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="af73c-462">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="af73c-463">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="af73c-463">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="af73c-464">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="af73c-464">Don't require passing parameters.</span></span>
* <span data-ttu-id="af73c-465">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-465">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="af73c-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="af73c-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="af73c-467">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="af73c-467">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="af73c-468">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="af73c-468">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="af73c-469">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="af73c-469">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="af73c-470">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="af73c-470">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="af73c-471">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="af73c-471">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="af73c-472">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-472">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="af73c-473">Mas os filtros diferem do middleware, pois fazem parte do tempo de execução, o que significa que eles têm acesso ao contexto e às construções.</span><span class="sxs-lookup"><span data-stu-id="af73c-473">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="af73c-474">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-474">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="af73c-475">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="af73c-475">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="af73c-476">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="af73c-476">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="af73c-477">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-477">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="af73c-478">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="af73c-478">Next actions</span></span>

* <span data-ttu-id="af73c-479">Consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="af73c-479">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="af73c-480">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="af73c-480">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af73c-481">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="af73c-481">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="af73c-482">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="af73c-482">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="af73c-483">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="af73c-483">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="af73c-484">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="af73c-484">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="af73c-485">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="af73c-485">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="af73c-486">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="af73c-486">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="af73c-487">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="af73c-487">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="af73c-488">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="af73c-488">Filters avoid duplicating code.</span></span> <span data-ttu-id="af73c-489">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="af73c-489">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="af73c-490">Este documento se aplica a Razor páginas, controladores de API e controladores com exibições.</span><span class="sxs-lookup"><span data-stu-id="af73c-490">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="af73c-491">[Exibir ou baixar exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="af73c-491">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="af73c-492">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="af73c-492">How filters work</span></span>

<span data-ttu-id="af73c-493">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="af73c-493">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="af73c-494">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="af73c-494">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="af73c-497">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-497">Filter types</span></span>

<span data-ttu-id="af73c-498">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="af73c-498">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="af73c-499">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="af73c-499">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="af73c-500">Os filtros de autorização podem causar um curto-circuito do pipeline se uma solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="af73c-500">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="af73c-501">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="af73c-501">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="af73c-502">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="af73c-502">Run after authorization.</span></span>  
  * <span data-ttu-id="af73c-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> pode executar o código antes do restante do pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="af73c-504">Por exemplo, `OnResourceExecuting` pode executar o código antes do model binding.</span><span class="sxs-lookup"><span data-stu-id="af73c-504">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="af73c-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> pode executar o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="af73c-506">[Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-506">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="af73c-507">Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-507">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="af73c-508">**Não** há suporte para filtros de ação em Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="af73c-508">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="af73c-509">[Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="af73c-509">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="af73c-510">[Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais.</span><span class="sxs-lookup"><span data-stu-id="af73c-510">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="af73c-511">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="af73c-511">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="af73c-512">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="af73c-512">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="af73c-513">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-513">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="af73c-516">Implementação</span><span class="sxs-lookup"><span data-stu-id="af73c-516">Implementation</span></span>

<span data-ttu-id="af73c-517">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="af73c-517">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="af73c-518">Os filtros síncronos podem executar o código antes (`On-Stage-Executing`) e depois (`On-Stage-Executed`) do estágio de pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-518">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="af73c-519">Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-519">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="af73c-520">`OnActionExecuted` é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="af73c-520">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="af73c-521">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="af73c-521">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="af73c-522">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-522">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="af73c-523">Cada um dos métodos `On-Stage-ExecutionAsync` usa um `FilterType-ExecutionDelegate` que executa o estágio de pipeline do filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-523">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="af73c-524">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-524">Multiple filter stages</span></span>

<span data-ttu-id="af73c-525">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="af73c-525">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="af73c-526">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.</span><span class="sxs-lookup"><span data-stu-id="af73c-526">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="af73c-527">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="af73c-527">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="af73c-528">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="af73c-528">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="af73c-529">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="af73c-529">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="af73c-530">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-530">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="af73c-531">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-531">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="af73c-532">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="af73c-532">Built-in filter attributes</span></span>

<span data-ttu-id="af73c-533">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="af73c-533">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="af73c-534">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="af73c-534">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="af73c-535">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="af73c-535">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="af73c-536">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="af73c-536">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="af73c-537">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="af73c-537">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="af73c-538">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="af73c-538">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="af73c-539">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="af73c-539">Filter scopes and order of execution</span></span>

<span data-ttu-id="af73c-540">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="af73c-540">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="af73c-541">Usando um atributo em uma ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-541">Using an attribute on an action.</span></span>
* <span data-ttu-id="af73c-542">Usando um atributo em um controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-542">Using an attribute on a controller.</span></span>
* <span data-ttu-id="af73c-543">Globalmente para todos os controladores e ações, como mostra o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="af73c-543">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="af73c-544">O código anterior adiciona três filtros globalmente usando a coleção [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="af73c-544">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="af73c-545">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="af73c-545">Default order of execution</span></span>

<span data-ttu-id="af73c-546">Quando há vários filtros *do mesmo tipo*, o escopo determina a ordem padrão de execução do filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-546">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="af73c-547">Filtros globais envolvem filtros de classe.</span><span class="sxs-lookup"><span data-stu-id="af73c-547">Global filters surround class filters.</span></span> <span data-ttu-id="af73c-548">Filtros de classe envolvem filtros de método.</span><span class="sxs-lookup"><span data-stu-id="af73c-548">Class filters surround method filters.</span></span>

<span data-ttu-id="af73c-549">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="af73c-549">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="af73c-550">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="af73c-550">The filter sequence:</span></span>

* <span data-ttu-id="af73c-551">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="af73c-551">The *before* code of global filters.</span></span>
  * <span data-ttu-id="af73c-552">O código *anterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-552">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="af73c-553">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-553">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="af73c-554">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-554">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="af73c-555">O código *posterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-555">The *after* code of controller filters.</span></span>
* <span data-ttu-id="af73c-556">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="af73c-556">The *after* code of global filters.</span></span>
  
<span data-ttu-id="af73c-557">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="af73c-557">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="af73c-558">Sequência</span><span class="sxs-lookup"><span data-stu-id="af73c-558">Sequence</span></span> | <span data-ttu-id="af73c-559">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-559">Filter scope</span></span> | <span data-ttu-id="af73c-560">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-560">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="af73c-561">1</span><span class="sxs-lookup"><span data-stu-id="af73c-561">1</span></span> | <span data-ttu-id="af73c-562">Global</span><span class="sxs-lookup"><span data-stu-id="af73c-562">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="af73c-563">2</span><span class="sxs-lookup"><span data-stu-id="af73c-563">2</span></span> | <span data-ttu-id="af73c-564">Controller</span><span class="sxs-lookup"><span data-stu-id="af73c-564">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="af73c-565">3</span><span class="sxs-lookup"><span data-stu-id="af73c-565">3</span></span> | <span data-ttu-id="af73c-566">Método</span><span class="sxs-lookup"><span data-stu-id="af73c-566">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="af73c-567">4</span><span class="sxs-lookup"><span data-stu-id="af73c-567">4</span></span> | <span data-ttu-id="af73c-568">Método</span><span class="sxs-lookup"><span data-stu-id="af73c-568">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="af73c-569">5</span><span class="sxs-lookup"><span data-stu-id="af73c-569">5</span></span> | <span data-ttu-id="af73c-570">Controller</span><span class="sxs-lookup"><span data-stu-id="af73c-570">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="af73c-571">6</span><span class="sxs-lookup"><span data-stu-id="af73c-571">6</span></span> | <span data-ttu-id="af73c-572">Global</span><span class="sxs-lookup"><span data-stu-id="af73c-572">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="af73c-573">Esta sequência mostra:</span><span class="sxs-lookup"><span data-stu-id="af73c-573">This sequence shows:</span></span>

* <span data-ttu-id="af73c-574">O filtro de método está aninhado no filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-574">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="af73c-575">O filtro de controlador está aninhado no filtro global.</span><span class="sxs-lookup"><span data-stu-id="af73c-575">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="af73c-576">Filtros de nível de página e de controlador Razor</span><span class="sxs-lookup"><span data-stu-id="af73c-576">Controller and Razor Page level filters</span></span>

<span data-ttu-id="af73c-577">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="af73c-577">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="af73c-578">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="af73c-578">These methods:</span></span>

* <span data-ttu-id="af73c-579">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-579">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="af73c-580">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-580">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="af73c-581">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-581">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="af73c-582">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-582">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="af73c-583">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-583">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="af73c-584">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="af73c-584">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="af73c-585">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="af73c-585">The `TestController`:</span></span>

* <span data-ttu-id="af73c-586">Aplica o `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à `FilterTest2` ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-586">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="af73c-587">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="af73c-587">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="af73c-588">Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="af73c-588">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="af73c-589">Para Razor páginas, consulte [implementar Razor filtros de página substituindo métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="af73c-589">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="af73c-590">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="af73c-590">Overriding the default order</span></span>

<span data-ttu-id="af73c-591">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-591">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="af73c-592">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="af73c-592">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="af73c-593">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="af73c-593">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="af73c-594">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="af73c-594">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="af73c-595">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="af73c-595">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="af73c-596">A propriedade `Order` pode ser definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="af73c-596">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="af73c-597">Considere os mesmos filtros de 3 ações mostrados no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="af73c-597">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="af73c-598">Se a propriedade `Order` do controlador e os filtros globais estiverem definidos como 1 e 2, respectivamente, a ordem de execução será invertida.</span><span class="sxs-lookup"><span data-stu-id="af73c-598">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="af73c-599">Sequência</span><span class="sxs-lookup"><span data-stu-id="af73c-599">Sequence</span></span> | <span data-ttu-id="af73c-600">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-600">Filter scope</span></span> | <span data-ttu-id="af73c-601">Propriedade `Order`</span><span class="sxs-lookup"><span data-stu-id="af73c-601">`Order` property</span></span> | <span data-ttu-id="af73c-602">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="af73c-602">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="af73c-603">1</span><span class="sxs-lookup"><span data-stu-id="af73c-603">1</span></span> | <span data-ttu-id="af73c-604">Método</span><span class="sxs-lookup"><span data-stu-id="af73c-604">Method</span></span> | <span data-ttu-id="af73c-605">0</span><span class="sxs-lookup"><span data-stu-id="af73c-605">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="af73c-606">2</span><span class="sxs-lookup"><span data-stu-id="af73c-606">2</span></span> | <span data-ttu-id="af73c-607">Controller</span><span class="sxs-lookup"><span data-stu-id="af73c-607">Controller</span></span> | <span data-ttu-id="af73c-608">1</span><span class="sxs-lookup"><span data-stu-id="af73c-608">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="af73c-609">3</span><span class="sxs-lookup"><span data-stu-id="af73c-609">3</span></span> | <span data-ttu-id="af73c-610">Global</span><span class="sxs-lookup"><span data-stu-id="af73c-610">Global</span></span> | <span data-ttu-id="af73c-611">2</span><span class="sxs-lookup"><span data-stu-id="af73c-611">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="af73c-612">4</span><span class="sxs-lookup"><span data-stu-id="af73c-612">4</span></span> | <span data-ttu-id="af73c-613">Global</span><span class="sxs-lookup"><span data-stu-id="af73c-613">Global</span></span> | <span data-ttu-id="af73c-614">2</span><span class="sxs-lookup"><span data-stu-id="af73c-614">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="af73c-615">5</span><span class="sxs-lookup"><span data-stu-id="af73c-615">5</span></span> | <span data-ttu-id="af73c-616">Controller</span><span class="sxs-lookup"><span data-stu-id="af73c-616">Controller</span></span> | <span data-ttu-id="af73c-617">1</span><span class="sxs-lookup"><span data-stu-id="af73c-617">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="af73c-618">6</span><span class="sxs-lookup"><span data-stu-id="af73c-618">6</span></span> | <span data-ttu-id="af73c-619">Método</span><span class="sxs-lookup"><span data-stu-id="af73c-619">Method</span></span> | <span data-ttu-id="af73c-620">0</span><span class="sxs-lookup"><span data-stu-id="af73c-620">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="af73c-621">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="af73c-621">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="af73c-622">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="af73c-622">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="af73c-623">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="af73c-623">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="af73c-624">Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="af73c-624">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="af73c-625">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="af73c-625">Cancellation and short-circuiting</span></span>

<span data-ttu-id="af73c-626">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-626">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="af73c-627">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="af73c-627">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="af73c-628">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="af73c-628">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="af73c-629">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="af73c-629">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="af73c-630">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-630">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="af73c-631">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-631">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="af73c-632">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="af73c-632">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="af73c-633">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="af73c-633">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="af73c-634">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="af73c-634">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="af73c-635">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="af73c-635">Dependency injection</span></span>

<span data-ttu-id="af73c-636">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="af73c-636">Filters can be added by type or by instance.</span></span> <span data-ttu-id="af73c-637">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="af73c-637">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="af73c-638">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="af73c-638">If a type is added, it's type-activated.</span></span> <span data-ttu-id="af73c-639">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="af73c-639">A type-activated filter means:</span></span>

* <span data-ttu-id="af73c-640">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="af73c-640">An instance is created for each request.</span></span>
* <span data-ttu-id="af73c-641">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="af73c-641">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="af73c-642">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="af73c-642">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="af73c-643">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="af73c-643">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="af73c-644">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="af73c-644">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="af73c-645">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="af73c-645">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="af73c-646">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="af73c-646">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="af73c-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="af73c-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="af73c-648">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="af73c-648">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="af73c-649">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-649">Loggers are available from DI.</span></span> <span data-ttu-id="af73c-650">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="af73c-650">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="af73c-651">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="af73c-651">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="af73c-652">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="af73c-652">Logging added to filters:</span></span>

* <span data-ttu-id="af73c-653">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-653">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="af73c-654">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="af73c-654">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="af73c-655">Os filtros internos registram ações e eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="af73c-655">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="af73c-656">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="af73c-656">ServiceFilterAttribute</span></span>

<span data-ttu-id="af73c-657">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="af73c-657">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="af73c-658">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-658">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="af73c-659">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="af73c-659">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="af73c-660">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="af73c-660">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="af73c-661">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="af73c-661">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="af73c-662">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="af73c-662">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="af73c-663">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-663">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="af73c-664">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="af73c-664">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="af73c-665">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-665">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="af73c-666">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="af73c-666">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="af73c-667">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="af73c-667">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="af73c-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="af73c-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="af73c-669">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="af73c-669">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="af73c-670">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-670">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="af73c-671">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="af73c-671">TypeFilterAttribute</span></span>

<span data-ttu-id="af73c-672">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="af73c-673">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="af73c-673">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="af73c-674">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="af73c-674">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="af73c-675">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-675">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="af73c-676">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-676">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="af73c-677">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="af73c-677">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="af73c-678">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="af73c-678">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="af73c-679">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-679">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="af73c-680">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="af73c-680">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="af73c-681">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="af73c-681">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="af73c-682">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="af73c-682">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="af73c-683">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="af73c-683">Authorization filters</span></span>

<span data-ttu-id="af73c-684">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="af73c-684">Authorization filters:</span></span>

* <span data-ttu-id="af73c-685">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-685">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="af73c-686">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-686">Control access to action methods.</span></span>
* <span data-ttu-id="af73c-687">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="af73c-687">Have a before method, but no after method.</span></span>

<span data-ttu-id="af73c-688">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="af73c-688">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="af73c-689">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="af73c-689">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="af73c-690">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="af73c-690">The built-in authorization filter:</span></span>

* <span data-ttu-id="af73c-691">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="af73c-691">Calls the authorization system.</span></span>
* <span data-ttu-id="af73c-692">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="af73c-692">Does not authorize requests.</span></span>

<span data-ttu-id="af73c-693">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="af73c-693">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="af73c-694">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="af73c-694">The exception will not be handled.</span></span>
* <span data-ttu-id="af73c-695">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-695">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="af73c-696">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="af73c-696">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="af73c-697">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="af73c-697">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="af73c-698">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="af73c-698">Resource filters</span></span>

<span data-ttu-id="af73c-699">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="af73c-699">Resource filters:</span></span>

* <span data-ttu-id="af73c-700">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="af73c-701">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-701">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="af73c-702">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="af73c-702">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="af73c-703">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-703">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="af73c-704">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="af73c-704">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="af73c-705">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="af73c-705">Resource filter examples:</span></span>

* <span data-ttu-id="af73c-706">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="af73c-706">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="af73c-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="af73c-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="af73c-708">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="af73c-708">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="af73c-709">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="af73c-709">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="af73c-710">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="af73c-710">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="af73c-711">Os filtros de ação **não** se aplicam a Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="af73c-711">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="af73c-712">Razor Páginas dão suporte a <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="af73c-712">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="af73c-713">Para obter mais informações, consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="af73c-713">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="af73c-714">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-714">Action filters:</span></span>

* <span data-ttu-id="af73c-715">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-715">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="af73c-716">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-716">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="af73c-717">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-717">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="af73c-718">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="af73c-718">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="af73c-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - permite a leitura das entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="af73c-720"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="af73c-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="af73c-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="af73c-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="af73c-722">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-722">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="af73c-723">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="af73c-723">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="af73c-724">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="af73c-724">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="af73c-725">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="af73c-725">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="af73c-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="af73c-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="af73c-728">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="af73c-728">Setting this property to null:</span></span>

  * <span data-ttu-id="af73c-729">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-729">Effectively handles the exception.</span></span>
  * <span data-ttu-id="af73c-730">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-730">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="af73c-731">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="af73c-731">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="af73c-732">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-732">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="af73c-733">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="af73c-733">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="af73c-734">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="af73c-734">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="af73c-735">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="af73c-735">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="af73c-736">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="af73c-736">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="af73c-737">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="af73c-737">Validate model state.</span></span>
* <span data-ttu-id="af73c-738">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="af73c-738">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="af73c-739">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="af73c-739">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="af73c-740">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="af73c-740">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="af73c-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="af73c-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="af73c-743">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="af73c-743">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="af73c-744">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-744">Effectively handles an exception.</span></span>
  * <span data-ttu-id="af73c-745">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-745">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="af73c-746">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="af73c-746">Exception filters</span></span>

<span data-ttu-id="af73c-747">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-747">Exception filters:</span></span>

* <span data-ttu-id="af73c-748">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="af73c-748">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="af73c-749">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="af73c-749">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="af73c-750">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="af73c-750">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="af73c-751">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-751">Exception filters:</span></span>

* <span data-ttu-id="af73c-752">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="af73c-752">Don't have before and after events.</span></span>
* <span data-ttu-id="af73c-753">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="af73c-753">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="af73c-754">Manipule exceções sem tratamento que ocorrem em Razor criação de página ou controlador, [Associação de modelo](xref:mvc/models/model-binding), filtros de ação ou métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-754">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="af73c-755">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="af73c-755">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="af73c-756">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="af73c-756">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="af73c-757">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-757">This stops propagation of the exception.</span></span> <span data-ttu-id="af73c-758">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="af73c-758">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="af73c-759">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="af73c-759">Only an action filter can do that.</span></span>

<span data-ttu-id="af73c-760">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="af73c-760">Exception filters:</span></span>

* <span data-ttu-id="af73c-761">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="af73c-761">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="af73c-762">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="af73c-762">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="af73c-763">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-763">Prefer middleware for exception handling.</span></span> <span data-ttu-id="af73c-764">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="af73c-764">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="af73c-765">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="af73c-765">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="af73c-766">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="af73c-766">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="af73c-767">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="af73c-767">Result filters</span></span>

<span data-ttu-id="af73c-768">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="af73c-768">Result filters:</span></span>

* <span data-ttu-id="af73c-769">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="af73c-769">Implement an interface:</span></span>
  * <span data-ttu-id="af73c-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="af73c-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="af73c-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="af73c-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="af73c-772">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-772">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="af73c-773">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="af73c-773">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="af73c-774">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="af73c-774">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="af73c-775">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-775">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="af73c-776">Uma ação que retorna um modo de exibição incluiria todo o processamento de Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> em execução.</span><span class="sxs-lookup"><span data-stu-id="af73c-776">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="af73c-777">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="af73c-777">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="af73c-778">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="af73c-778">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="af73c-779">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-779">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="af73c-780">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="af73c-780">Result filters are not executed when:</span></span>

* <span data-ttu-id="af73c-781">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-781">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="af73c-782">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-782">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="af73c-783">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="af73c-783">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="af73c-784">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="af73c-784">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="af73c-785">A geração de uma exceção em `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="af73c-785">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="af73c-786">Impedir a execução do resultado da ação e dos próximos filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-786">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="af73c-787">Ser tratada como uma falha e não como um resultado com êxito.</span><span class="sxs-lookup"><span data-stu-id="af73c-787">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="af73c-788">Quando o <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> método é executado, a resposta provavelmente já foi enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="af73c-788">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="af73c-789">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada ainda mais.</span><span class="sxs-lookup"><span data-stu-id="af73c-789">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="af73c-790">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-790">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="af73c-791">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-791">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="af73c-792">Definir `Exception` como nulo trata uma exceção com eficiência e impede que ela seja gerada novamente pelo ASP.NET Core posteriormente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-792">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="af73c-793">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="af73c-793">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="af73c-794">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="af73c-794">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="af73c-795">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-795">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="af73c-796">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="af73c-796">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="af73c-797">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="af73c-797">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="af73c-798">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="af73c-798">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="af73c-799">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="af73c-799">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="af73c-800">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="af73c-800">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="af73c-801">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="af73c-801">This includes action results produced by:</span></span>

* <span data-ttu-id="af73c-802">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="af73c-802">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="af73c-803">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="af73c-803">Exception filters.</span></span>

<span data-ttu-id="af73c-804">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="af73c-804">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="af73c-805">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="af73c-805">IFilterFactory</span></span>

<span data-ttu-id="af73c-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="af73c-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="af73c-807">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="af73c-807">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="af73c-808">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="af73c-808">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="af73c-809">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="af73c-809">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="af73c-810">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="af73c-810">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="af73c-811">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="af73c-811">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="af73c-812">O código anterior pode ser testado executando o [exemplo para download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="af73c-812">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="af73c-813">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="af73c-813">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="af73c-814">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="af73c-814">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="af73c-815">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="af73c-815">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="af73c-816">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="af73c-816">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="af73c-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="af73c-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="af73c-818">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="af73c-818">**internal:** `My header`</span></span>

<span data-ttu-id="af73c-819">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="af73c-819">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="af73c-820">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="af73c-820">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="af73c-821">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="af73c-821">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="af73c-822">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="af73c-822">Don't require passing parameters.</span></span>
* <span data-ttu-id="af73c-823">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="af73c-823">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="af73c-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="af73c-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="af73c-825">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="af73c-825">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="af73c-826">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="af73c-826">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="af73c-827">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="af73c-827">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="af73c-828">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="af73c-828">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="af73c-829">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="af73c-829">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="af73c-830">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-830">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="af73c-831">Mas os filtros diferem do middleware porque fazem parte do ASP.NET Core, o que significa que têm acesso ao contexto e a constructos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-831">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="af73c-832">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="af73c-832">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="af73c-833">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="af73c-833">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="af73c-834">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="af73c-834">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="af73c-835">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="af73c-835">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="af73c-836">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="af73c-836">Next actions</span></span>

* <span data-ttu-id="af73c-837">Consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="af73c-837">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="af73c-838">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="af73c-838">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
