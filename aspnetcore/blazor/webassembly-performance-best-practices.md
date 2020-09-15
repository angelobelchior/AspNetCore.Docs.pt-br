---
title: Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core
author: pranavkm
description: Dicas para aumentar o desempenho em Blazor WebAssembly aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 5d3cd1480dd37f437b2d6d5a89af0a842286be95
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080258"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="d2bfe-103">Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2bfe-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="d2bfe-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="d2bfe-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="d2bfe-105">Este artigo fornece diretrizes para ASP.NET Core Blazor WebAssembly práticas recomendadas de desempenho.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="d2bfe-106">Evitar renderizações de componente desnecessárias</span><span class="sxs-lookup"><span data-stu-id="d2bfe-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="d2bfe-107">Blazoro algoritmo de diferenciação do evita a rerenderização de um componente quando o algoritmo percebe que o componente não foi alterado.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="d2bfe-108">Substitua o <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> controle refinado sobre a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="d2bfe-109">Se criar um componente somente de interface do usuário que nunca é alterado após a renderização inicial, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para retornar `false` :</span><span class="sxs-lookup"><span data-stu-id="d2bfe-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="d2bfe-110">A maioria dos aplicativos não requer controle refinado, mas <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pode ser usada para renderizar seletivamente um componente que responde a um evento de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="d2bfe-111">Usar <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> também pode ser importante em cenários em que um grande número de componentes é renderizado.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="d2bfe-112">Considere uma grade, em que o uso de <xref:Microsoft.AspNetCore.Components.EventCallback> em um componente em uma célula da grade é chamado <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> na grade.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="d2bfe-113"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>A chamada causa um novo processamento de cada componente filho.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="d2bfe-114">Se apenas um pequeno número de células exigir rerenderização, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para evitar a penalidade de desempenho de renderizações desnecessárias.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="d2bfe-115">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d2bfe-115">In the following example:</span></span>

* <span data-ttu-id="d2bfe-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é substituído e definido como o valor do <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, que é inicialmente `false` quando o componente é carregado.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="d2bfe-117">Quando o botão é selecionado, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é definido como `true` , o que força o componente a renderizar novamente com o atualizado `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="d2bfe-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="d2bfe-118">Imediatamente após o reprocessamento, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> o define o valor de <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> voltar para `false` para evitar a rerenderização posterior até a próxima vez que o botão for selecionado.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="d2bfe-119">Para obter mais informações, consulte <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="d2bfe-120">Virtualizar fragmentos reutilizáveis</span><span class="sxs-lookup"><span data-stu-id="d2bfe-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="d2bfe-121">Os componentes oferecem uma abordagem conveniente para produzir fragmentos reutilizáveis de código e marcação.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="d2bfe-122">Em geral, é recomendável criar componentes individuais que melhor se alinhem com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="d2bfe-123">Uma limitação é que cada componente filho adicional contribui para o tempo total que leva para renderizar um componente pai.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="d2bfe-124">Para a maioria dos aplicativos, a sobrecarga adicional é insignificante.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="d2bfe-125">Os aplicativos que produzem um grande número de componentes devem considerar o uso de estratégias para reduzir a sobrecarga de processamento, como limitar o número de componentes renderizados.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="d2bfe-126">Por exemplo, uma grade ou lista que renderiza centenas de linhas que contêm componentes tem uso intensivo de processador para renderização.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="d2bfe-127">Considere a possibilidade de virtualizar um layout de grade ou lista para que apenas um subconjunto dos componentes seja renderizado em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="d2bfe-128">Para obter um exemplo de renderização de subconjunto de componentes, consulte os seguintes componentes no [ `Virtualization` aplicativo de exemplo (repositório GitHub ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="d2bfe-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="d2bfe-129">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): um componente escrito em C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para renderizar um conjunto de linhas de dados meteorológicos com base na rolagem do usuário.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="d2bfe-130">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa o `Virtualize` componente para exibir 25 linhas de dados meteorológicos por vez.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="d2bfe-131">Evitar a interoperabilidade JavaScript para empacotar dados</span><span class="sxs-lookup"><span data-stu-id="d2bfe-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="d2bfe-132">No Blazor WebAssembly , uma chamada de interoperabilidade JavaScript (js) deve atravessar o limite Webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="d2bfe-133">A serialização e a desserialização de conteúdo entre os dois contextos cria sobrecarga de processamento para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="d2bfe-134">As chamadas de interoperabilidade do JS frequentes muitas vezes afetam o desempenho.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="d2bfe-135">Para reduzir o marshaling dos dados em todo o limite, determine se o aplicativo pode consolidar muitas cargas pequenas em uma única carga grande para evitar o alto volume de alternância de contexto entre Webassembly e JS.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="d2bfe-136">Usar System.Text.Jsem</span><span class="sxs-lookup"><span data-stu-id="d2bfe-136">Use System.Text.Json</span></span>

<span data-ttu-id="d2bfe-137">Blazora implementação de interoperabilidade do JS depende de <xref:System.Text.Json> , que é uma biblioteca de SERIALIZAÇÃO JSON de alto desempenho com alocação de memória insuficiente.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="d2bfe-138">O uso <xref:System.Text.Json> do não resulta em tamanho de carga de aplicativo adicional ao adicionar uma ou mais bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="d2bfe-139">Para obter diretrizes de migração, consulte [como migrar do `Newtonsoft.Json` para `System.Text.Json` o ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="d2bfe-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="d2bfe-140">Usar APIs de interoperabilidade do JS síncronas e não marshalled, quando apropriado</span><span class="sxs-lookup"><span data-stu-id="d2bfe-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="d2bfe-141">Blazor WebAssembly o oferece duas versões adicionais do <xref:Microsoft.JSInterop.IJSRuntime> sobre a única versão disponível para Blazor Server aplicativos:</span><span class="sxs-lookup"><span data-stu-id="d2bfe-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="d2bfe-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> permite invocar chamadas de interoperabilidade JS de forma síncrona, que tem menos sobrecarga do que as versões assíncronas:</span><span class="sxs-lookup"><span data-stu-id="d2bfe-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="d2bfe-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permite chamadas de interoperabilidade JS não marshalled:</span><span class="sxs-lookup"><span data-stu-id="d2bfe-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="d2bfe-144">Embora o uso do <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> tenha a menor sobrecarga das abordagens de interoperabilidade do js, as APIs de JavaScript necessárias para interagir com essas APIs não estão documentadas no momento e estão sujeitas a alterações significativas em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="d2bfe-145">Reduzir o tamanho do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2bfe-145">Reduce app size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="d2bfe-146">Corte de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="d2bfe-146">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="d2bfe-147">A [corte de assemblies não utilizados de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-trimmer) reduz o tamanho do aplicativo removendo o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-147">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="d2bfe-148">Por padrão, o corte é executado durante a publicação de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-148">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="d2bfe-149">Para se beneficiar da remoção, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :</span><span class="sxs-lookup"><span data-stu-id="d2bfe-149">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="d2bfe-150">Vinculação de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="d2bfe-150">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="d2bfe-151">A [vinculação de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-linker) reduz o tamanho do aplicativo ao cortar o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-151">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="d2bfe-152">Por padrão, o vinculador de linguagem intermediária (IL) só é habilitado durante a criação da `Release` configuração.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-152">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="d2bfe-153">Para se beneficiar disso, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :</span><span class="sxs-lookup"><span data-stu-id="d2bfe-153">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="d2bfe-154">Assemblies de carga lenta</span><span class="sxs-lookup"><span data-stu-id="d2bfe-154">Lazy load assemblies</span></span>

<span data-ttu-id="d2bfe-155">Carregar assemblies em tempo de execução quando os assemblies são exigidos por uma rota.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-155">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="d2bfe-156">Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-156">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="d2bfe-157">Compactação</span><span class="sxs-lookup"><span data-stu-id="d2bfe-157">Compression</span></span>

<span data-ttu-id="d2bfe-158">Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-158">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="d2bfe-159">Blazor depende do servidor para executar negotation de conteúdo e fornecer arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-159">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="d2bfe-160">Depois que um aplicativo for implantado, verifique se o aplicativo serve arquivos compactados.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-160">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="d2bfe-161">Inspecione a guia rede no Ferramentas para Desenvolvedores de um navegador e verifique se os arquivos são servidos com `Content-Encoding: br` ou `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="d2bfe-161">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="d2bfe-162">Se o host não estiver servindo arquivos compactados, siga as instruções em <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="d2bfe-162">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="d2bfe-163">Desabilitar recursos não utilizados</span><span class="sxs-lookup"><span data-stu-id="d2bfe-163">Disable unused features</span></span>

<span data-ttu-id="d2bfe-164">Blazor WebAssemblyo tempo de execução do inclui os seguintes recursos do .NET que podem ser desabilitados se o aplicativo não exigir um tamanho de carga menor:</span><span class="sxs-lookup"><span data-stu-id="d2bfe-164">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="d2bfe-165">Um arquivo de dados está incluído para tornar as informações de fuso horário corretas.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-165">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="d2bfe-166">Se o aplicativo não exigir esse recurso, considere a possibilidade de desabilitá-lo definindo a `BlazorEnableTimeZoneSupport` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="d2bfe-166">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="d2bfe-167">Por padrão, Blazor WebAssembly o transporta os recursos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-167">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="d2bfe-168">Se o aplicativo não exigir localização, você poderá [Configurar o aplicativo para dar suporte à cultura invariável](xref:blazor/globalization-localization), que se baseia na `en-US` cultura:</span><span class="sxs-lookup"><span data-stu-id="d2bfe-168">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="d2bfe-169">As informações de agrupamento estão incluídas para fazer APIs como o <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionamento correto.</span><span class="sxs-lookup"><span data-stu-id="d2bfe-169">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="d2bfe-170">Se você tiver certeza de que o aplicativo não exige os dados de agrupamento, considere desabilitá-lo definindo a `BlazorWebAssemblyPreserveCollationData` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="d2bfe-170">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
