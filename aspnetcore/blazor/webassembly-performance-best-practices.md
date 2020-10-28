---
title: :::no-loc(Blazor WebAssembly):::Práticas recomendadas de desempenho de ASP.NET Core
author: pranavkm
description: 'Dicas para aumentar o desempenho em :::no-loc(Blazor WebAssembly)::: aplicativos ASP.NET Core e evitar problemas comuns de desempenho.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 0e827680e7024eabed09b989466476a3a80eb225
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690265"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="68b22-103">:::no-loc(Blazor WebAssembly):::Práticas recomendadas de desempenho de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68b22-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: performance best practices</span></span>

<span data-ttu-id="68b22-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm) e [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="68b22-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="68b22-105">:::no-loc(Blazor WebAssembly)::: é cuidadosamente projetado e otimizado para permitir alto desempenho na maioria dos cenários de interface do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68b22-105">:::no-loc(Blazor WebAssembly)::: is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="68b22-106">No entanto, a produção dos melhores resultados depende dos desenvolvedores que usam os padrões e recursos certos.</span><span class="sxs-lookup"><span data-stu-id="68b22-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="68b22-107">Considere os seguintes aspectos:</span><span class="sxs-lookup"><span data-stu-id="68b22-107">Consider the following aspects:</span></span>

* <span data-ttu-id="68b22-108">**Taxa de transferência de tempo de execução** : o código .net é executado em um intérprete no tempo de execução do Webassembly, portanto, a taxa de transferência da CPU é limitada</span><span class="sxs-lookup"><span data-stu-id="68b22-108">**Runtime throughput** : The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="68b22-109">Em cenários exigentes, o aplicativo se beneficia da [otimização da velocidade de renderização](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="68b22-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="68b22-110">**Tempo de inicialização** : o aplicativo transfere um tempo de execução do .net para o navegador, portanto, é importante usar recursos que [minimizem o tamanho do download do aplicativo](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="68b22-110">**Startup time** : The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="68b22-111">Otimizar a velocidade de renderização</span><span class="sxs-lookup"><span data-stu-id="68b22-111">Optimize rendering speed</span></span>

<span data-ttu-id="68b22-112">As seções a seguir fornecem recomendações para minimizar a carga de trabalho de renderização e melhorar a capacidade de resposta da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="68b22-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="68b22-113">Seguir esse aviso pode facilmente fazer uma *melhoria de dez vezes ou mais* em velocidades de renderização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="68b22-114">Evitar renderização desnecessária de subárvores de componentes</span><span class="sxs-lookup"><span data-stu-id="68b22-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="68b22-115">Em tempo de execução, os componentes existem como uma hierarquia.</span><span class="sxs-lookup"><span data-stu-id="68b22-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="68b22-116">Um componente raiz tem componentes filhos.</span><span class="sxs-lookup"><span data-stu-id="68b22-116">A root component has child components.</span></span> <span data-ttu-id="68b22-117">Por sua vez, os filhos da raiz têm seus próprios componentes filho e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="68b22-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="68b22-118">Quando ocorre um evento, como um usuário selecionando um botão, é assim :::no-loc(Blazor)::: que decide quais componentes serão renderizados novamente:</span><span class="sxs-lookup"><span data-stu-id="68b22-118">When an event occurs, such as a user selecting a button, this is how :::no-loc(Blazor)::: decides which components to rerender:</span></span>

 1. <span data-ttu-id="68b22-119">O evento em si é expedido para qualquer componente que renderizasse o manipulador do evento.</span><span class="sxs-lookup"><span data-stu-id="68b22-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="68b22-120">Depois de executar o manipulador de eventos, esse componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="68b22-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="68b22-121">Sempre que qualquer componente for rerenderizado, ele fornecerá uma nova cópia dos valores de parâmetro para cada um de seus componentes filho.</span><span class="sxs-lookup"><span data-stu-id="68b22-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="68b22-122">Ao receber um novo conjunto de valores de parâmetro, cada componente escolhe se deseja rerenderizar.</span><span class="sxs-lookup"><span data-stu-id="68b22-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="68b22-123">Por padrão, os componentes são reprocessados se os valores de parâmetro podem ter sido alterados (por exemplo, se forem objetos mutáveis).</span><span class="sxs-lookup"><span data-stu-id="68b22-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="68b22-124">As duas últimas etapas dessa sequência continuam recursivamente a hierarquia de componentes.</span><span class="sxs-lookup"><span data-stu-id="68b22-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="68b22-125">Em muitos casos, toda a subárvore é reprocessada.</span><span class="sxs-lookup"><span data-stu-id="68b22-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="68b22-126">Isso significa que os eventos que visam componentes de alto nível podem causar processos dispendiosos de reprocessamento porque tudo abaixo desse ponto deve ser reprocessado.</span><span class="sxs-lookup"><span data-stu-id="68b22-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="68b22-127">Se você quiser interromper esse processo e evitar a recursão de renderização em uma subárvore específica, poderá:</span><span class="sxs-lookup"><span data-stu-id="68b22-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="68b22-128">Verifique se todos os parâmetros para um determinado componente são de tipos irmutáveis primitivos (por exemplo,,,, `string` `int` `bool` `DateTime` e outros).</span><span class="sxs-lookup"><span data-stu-id="68b22-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="68b22-129">A lógica interna para detectar alterações automaticamente ignora a rerenderização se nenhum desses valores de parâmetro foram alterados.</span><span class="sxs-lookup"><span data-stu-id="68b22-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="68b22-130">Se você renderizar um componente filho com `<Customer CustomerId="@item.CustomerId" />` , em que `CustomerId` é um `int` valor, ele não será renderizado, exceto quando houver `item.CustomerId` alterações.</span><span class="sxs-lookup"><span data-stu-id="68b22-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="68b22-131">Se você precisar aceitar valores de parâmetro não primitivos, como tipos de modelo personalizados, retornos de chamada de evento ou <xref:Microsoft.AspNetCore.Components.RenderFragment> valores, você poderá substituir <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para controlar a decisão sobre se deve renderizar, que é descrito no [uso da `ShouldRender` ](#use-of-shouldrender) seção.</span><span class="sxs-lookup"><span data-stu-id="68b22-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="68b22-132">Ao ignorar a rerenderização de subárvores inteiras, você poderá remover a grande maioria do custo de renderização quando ocorrer um evento.</span><span class="sxs-lookup"><span data-stu-id="68b22-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="68b22-133">Talvez você queira fatorar os componentes filho especificamente para que possa ignorar a rerenderização dessa parte da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="68b22-134">Essa é uma maneira válida de reduzir o custo de renderização de um componente pai.</span><span class="sxs-lookup"><span data-stu-id="68b22-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="68b22-135">Uso de `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="68b22-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="68b22-136">Se criar um componente somente de interface do usuário que nunca é alterado após a renderização inicial (independentemente de quaisquer valores de parâmetro), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para retornar `false` :</span><span class="sxs-lookup"><span data-stu-id="68b22-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="68b22-137">Se o componente requer apenas a rerenderização quando seus valores de parâmetro se modificam de formas específicas, você pode usar campos privados para controlar as informações necessárias para detectar alterações.</span><span class="sxs-lookup"><span data-stu-id="68b22-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="68b22-138">No exemplo a seguir, `shouldRender` é baseado na verificação de qualquer tipo de alteração ou mutação que deve solicitar uma rerenderização.</span><span class="sxs-lookup"><span data-stu-id="68b22-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="68b22-139">`prevOutboundFlightId` e `prevInboundFlightId` rastreie informações para a próxima atualização em potencial:</span><span class="sxs-lookup"><span data-stu-id="68b22-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="68b22-140">No código anterior, um manipulador de eventos também pode ser definido `shouldRender` como para `true` que o componente seja reprocessado após o evento.</span><span class="sxs-lookup"><span data-stu-id="68b22-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="68b22-141">Para a maioria dos componentes, esse nível de controle manual não é necessário.</span><span class="sxs-lookup"><span data-stu-id="68b22-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="68b22-142">Você só deve se preocupar em ignorar as subárvores de renderização se essas subárvores forem particularmente caras de renderizar e estiverem causando atraso na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="68b22-143">Para obter mais informações, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="68b22-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="68b22-144">Virtualização</span><span class="sxs-lookup"><span data-stu-id="68b22-144">Virtualization</span></span>

<span data-ttu-id="68b22-145">Ao renderizar grandes quantidades de interface do usuário dentro de um loop, por exemplo, uma lista ou grade com milhares de entradas, a quantidade enorme de operações de renderização pode levar a um retardo na renderização da interface do usuário e, portanto, uma experiência ruim para os usuários.</span><span class="sxs-lookup"><span data-stu-id="68b22-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="68b22-146">Considerando que o usuário só pode ver um pequeno número de elementos de uma só vez sem rolagem, parece que é dispendioso gastar muito tempo renderizando elementos que não estão visíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="68b22-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="68b22-147">Para resolver isso, o :::no-loc(Blazor)::: fornece um [ `<Virtualize>` componente](xref:blazor/components/virtualization) interno que cria os comportamentos de aparência e rolagem de uma lista arbitrariamente grande, mas, na verdade, apenas renderiza os itens de lista que estão dentro do visor de rolagem atual.</span><span class="sxs-lookup"><span data-stu-id="68b22-147">To address this, :::no-loc(Blazor)::: provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="68b22-148">Por exemplo, isso significa que o aplicativo pode ter uma lista com 100.000 entradas, mas apenas pagar o custo de renderização de 20 itens visíveis a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="68b22-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="68b22-149">O uso do `<Virtualize>` componente pode escalar verticalmente o desempenho da interface do usuário em ordens de magnitude.</span><span class="sxs-lookup"><span data-stu-id="68b22-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="68b22-150">`<Virtualize>` pode ser usado quando:</span><span class="sxs-lookup"><span data-stu-id="68b22-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="68b22-151">Renderizando um conjunto de itens de dados em um loop.</span><span class="sxs-lookup"><span data-stu-id="68b22-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="68b22-152">A maioria dos itens não está visível devido à rolagem.</span><span class="sxs-lookup"><span data-stu-id="68b22-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="68b22-153">Os itens processados são exatamente do mesmo tamanho.</span><span class="sxs-lookup"><span data-stu-id="68b22-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="68b22-154">Quando o usuário rola para um ponto arbitrário, o componente pode calcular os itens visíveis a serem mostrados.</span><span class="sxs-lookup"><span data-stu-id="68b22-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="68b22-155">Veja a seguir um exemplo de uma lista não virtualizada:</span><span class="sxs-lookup"><span data-stu-id="68b22-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="68b22-156">Se a `allFlights` coleção mantiver 10.000 itens, ela instancia e renderiza as `<FlightSummary>` instâncias de componente 10.000.</span><span class="sxs-lookup"><span data-stu-id="68b22-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="68b22-157">Em comparação, o seguinte mostra um exemplo de uma lista virtualizada:</span><span class="sxs-lookup"><span data-stu-id="68b22-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="68b22-158">Embora a interface do usuário resultante tenha a mesma aparência de um usuário, por trás dos bastidores, o componente só instancia e renderiza quantas `<FlightSummary>` instâncias forem necessárias para preencher a região rolável.</span><span class="sxs-lookup"><span data-stu-id="68b22-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="68b22-159">O conjunto de `<FlightSummary>` instâncias exibidas é recalculado e renderizado conforme o usuário rola.</span><span class="sxs-lookup"><span data-stu-id="68b22-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="68b22-160">`<Virtualize>` também tem outros benefícios.</span><span class="sxs-lookup"><span data-stu-id="68b22-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="68b22-161">Por exemplo, quando um componente solicita dados de uma API externa, `<Virtualize>` permite que o componente busque apenas a fatia de registros que correspondem à região visível atual, em vez de baixar todos os dados da coleção.</span><span class="sxs-lookup"><span data-stu-id="68b22-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="68b22-162">Para obter mais informações, consulte <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="68b22-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="68b22-163">Crie componentes leves e otimizados</span><span class="sxs-lookup"><span data-stu-id="68b22-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="68b22-164">A maioria dos :::no-loc(Blazor)::: componentes não exige esforços agressivos de otimização.</span><span class="sxs-lookup"><span data-stu-id="68b22-164">Most :::no-loc(Blazor)::: components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="68b22-165">Isso ocorre porque a maioria dos componentes geralmente não se repete na interface do usuário e não reprocessa em alta frequência.</span><span class="sxs-lookup"><span data-stu-id="68b22-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="68b22-166">Por exemplo, `@page` componentes e componentes que representam partes de interface de usuário de alto nível, como caixas de diálogo ou formulários, provavelmente aparecem apenas um de cada vez e apenas rerenderizam em resposta a um gesto de usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="68b22-167">Esses componentes não criam uma carga de trabalho de renderização alta, portanto, você pode usar livremente qualquer combinação de recursos de estrutura que desejar sem se preocupar muito com o desempenho de renderização.</span><span class="sxs-lookup"><span data-stu-id="68b22-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="68b22-168">No entanto, também há cenários comuns em que você cria componentes que precisam ser repetidos em escala.</span><span class="sxs-lookup"><span data-stu-id="68b22-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="68b22-169">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="68b22-169">For example:</span></span>

 * <span data-ttu-id="68b22-170">Formulários aninhados grandes podem ter centenas de entradas, rótulos e outros elementos individuais.</span><span class="sxs-lookup"><span data-stu-id="68b22-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="68b22-171">As grades podem ter milhares de células.</span><span class="sxs-lookup"><span data-stu-id="68b22-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="68b22-172">As plotagens de dispersão podem ter milhões de pontos de dados.</span><span class="sxs-lookup"><span data-stu-id="68b22-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="68b22-173">Se modelar cada unidade como instâncias de componente separadas, haverá muitos deles que o desempenho de renderização se torna crítico.</span><span class="sxs-lookup"><span data-stu-id="68b22-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="68b22-174">Esta seção fornece conselhos sobre como tornar esses componentes leves para que a interface do usuário permaneça rápida e responsiva.</span><span class="sxs-lookup"><span data-stu-id="68b22-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="68b22-175">Evite milhares de instâncias de componentes</span><span class="sxs-lookup"><span data-stu-id="68b22-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="68b22-176">Cada componente é uma ilha separada que pode ser renderizada independentemente de seus pais e filhos.</span><span class="sxs-lookup"><span data-stu-id="68b22-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="68b22-177">Ao escolher como dividir a interface do usuário em uma hierarquia de componentes, você está tomando controle sobre a granularidade da renderização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="68b22-178">Isso pode ser bom ou ruim para o desempenho.</span><span class="sxs-lookup"><span data-stu-id="68b22-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="68b22-179">Dividindo a interface do usuário em mais componentes, você pode ter partes menores da rerenderização da interface do usuário quando ocorrem eventos.</span><span class="sxs-lookup"><span data-stu-id="68b22-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="68b22-180">Por exemplo, quando um usuário clica em um botão em uma linha de tabela, você pode ter apenas a rerenderização de linha única em vez de toda a página ou tabela.</span><span class="sxs-lookup"><span data-stu-id="68b22-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="68b22-181">No entanto, cada componente extra envolve alguma sobrecarga extra de memória e CPU para lidar com seu estado independente e ciclo de vida de renderização.</span><span class="sxs-lookup"><span data-stu-id="68b22-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="68b22-182">Ao ajustar o desempenho do :::no-loc(Blazor WebAssembly)::: no .NET 5, medimos uma sobrecarga de renderização de cerca de 0, 6 ms por instância de componente.</span><span class="sxs-lookup"><span data-stu-id="68b22-182">When tuning the performance of :::no-loc(Blazor WebAssembly)::: on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="68b22-183">Isso se baseia em um componente simples que aceita três parâmetros em execução em um laptop típico.</span><span class="sxs-lookup"><span data-stu-id="68b22-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="68b22-184">Internamente, a sobrecarga está em grande parte devido à recuperação do estado por componente de dicionários e passagem e recebimento de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="68b22-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="68b22-185">Por multiplicação, você pode ver que a adição de 2.000 instâncias de componente extras adicionará 0,12 segundos ao tempo de renderização e a interface do usuário começaria a ficar lenta para os usuários.</span><span class="sxs-lookup"><span data-stu-id="68b22-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="68b22-186">É possível tornar os componentes mais leves para que você possa ter mais deles, mas, em geral, a técnica mais poderosa não tem tantos componentes.</span><span class="sxs-lookup"><span data-stu-id="68b22-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="68b22-187">As seções a seguir descrevem duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="68b22-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="68b22-188">Componentes filho embutidos em seus pais</span><span class="sxs-lookup"><span data-stu-id="68b22-188">Inline child components into their parents</span></span>

<span data-ttu-id="68b22-189">Considere o seguinte componente que renderiza uma sequência de componentes filho:</span><span class="sxs-lookup"><span data-stu-id="68b22-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="68b22-190">Para o código de exemplo anterior, o `<ChatMessageDisplay>` componente é definido em um arquivo `ChatMessageDisplay.razor` que contém:</span><span class="sxs-lookup"><span data-stu-id="68b22-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="68b22-191">O exemplo anterior funciona bem e tem um bom desempenho, desde que milhares de mensagens não sejam mostradas ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="68b22-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="68b22-192">Para mostrar milhares de mensagens de uma vez, considere *não* fatorar o `ChatMessageDisplay` componente separado.</span><span class="sxs-lookup"><span data-stu-id="68b22-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="68b22-193">Em vez disso, embutimos a renderização diretamente no pai:</span><span class="sxs-lookup"><span data-stu-id="68b22-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="68b22-194">Isso evita a sobrecarga por componente da renderização de forma que muitos componentes filho tenham o custo de não serem capazes de reprocessar cada um deles de forma independente.</span><span class="sxs-lookup"><span data-stu-id="68b22-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="68b22-195">Definir reutilizável `RenderFragments` no código</span><span class="sxs-lookup"><span data-stu-id="68b22-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="68b22-196">Você pode estar decompondo componentes filho puramente como uma maneira de reutilizar a lógica de renderização.</span><span class="sxs-lookup"><span data-stu-id="68b22-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="68b22-197">Se esse for o caso, ainda é possível reutilizar a lógica de renderização sem declarar os componentes reais.</span><span class="sxs-lookup"><span data-stu-id="68b22-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="68b22-198">No bloco de qualquer componente `@code` , você pode definir um <xref:Microsoft.AspNetCore.Components.RenderFragment> que emita a interface do usuário e que possa ser chamado de qualquer lugar:</span><span class="sxs-lookup"><span data-stu-id="68b22-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is :::no-loc(Blazor)::: working for you?" />
        </div>
    };
}
```

<span data-ttu-id="68b22-199">Como demonstated no exemplo anterior, os componentes podem emitir marcação a partir do código dentro de seu `@code` bloco e fora dele.</span><span class="sxs-lookup"><span data-stu-id="68b22-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="68b22-200">Essa abordagem define um <xref:Microsoft.AspNetCore.Components.RenderFragment> delegado que você pode renderizar dentro da saída de renderização normal do componente, opcionalmente em vários lugares.</span><span class="sxs-lookup"><span data-stu-id="68b22-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="68b22-201">É necessário que o delegado aceite um parâmetro chamado `__builder` do tipo <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para que o :::no-loc(Razor)::: compilador possa produzir instruções de renderização para ele.</span><span class="sxs-lookup"><span data-stu-id="68b22-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the :::no-loc(Razor)::: compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="68b22-202">Se você quiser tornar isso reutilizável em vários componentes, considere declará-lo como um `public static` membro:</span><span class="sxs-lookup"><span data-stu-id="68b22-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="68b22-203">Agora, isso pode ser invocado a partir de um componente não relacionado.</span><span class="sxs-lookup"><span data-stu-id="68b22-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="68b22-204">Essa técnica é útil para criar bibliotecas de trechos de marcação reutilizáveis que são renderizados sem nenhuma sobrecarga por componente.</span><span class="sxs-lookup"><span data-stu-id="68b22-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="68b22-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> os delegados também podem aceitar parâmetros.</span><span class="sxs-lookup"><span data-stu-id="68b22-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="68b22-206">Para criar o equivalente do `ChatMessageDisplay` componente do exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="68b22-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="68b22-207">Essa abordagem fornece o benefício de reutilizar a lógica de renderização sem sobrecarga por componente.</span><span class="sxs-lookup"><span data-stu-id="68b22-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="68b22-208">No entanto, ele não tem o benefício de ser capaz de atualizar sua subárvore da interface do usuário de forma independente, nem tem a capacidade de ignorar o processamento dessa subárvore da interface do usuário quando seu pai é renderizado, já que não há nenhum limite de componente.</span><span class="sxs-lookup"><span data-stu-id="68b22-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="68b22-209">Não receber muitos parâmetros</span><span class="sxs-lookup"><span data-stu-id="68b22-209">Don't receive too many parameters</span></span>

<span data-ttu-id="68b22-210">Se um componente se repetir de forma extremamente frequente, por exemplo, centenas ou milhares de vezes, lembre-se de que a sobrecarga de passar e receber cada parâmetro se baseia.</span><span class="sxs-lookup"><span data-stu-id="68b22-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="68b22-211">É raro que muitos parâmetros restringem seriamente o desempenho, mas pode ser um fator.</span><span class="sxs-lookup"><span data-stu-id="68b22-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="68b22-212">Para um `<TableCell>` componente que renderiza 1.000 vezes em uma grade, cada parâmetro extra passado para ele poderia adicionar cerca de 15 ms ao custo total de renderização.</span><span class="sxs-lookup"><span data-stu-id="68b22-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="68b22-213">Se cada célula aceitasse 10 parâmetros, a passagem de parâmetro leva cerca de 150 ms por renderização de componente e, portanto, talvez 150.000 MS (150 segundos) e, por sua própria causa, uma interface de usuário com retardo.</span><span class="sxs-lookup"><span data-stu-id="68b22-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="68b22-214">Para reduzir essa carga, você pode reunir vários parâmetros por meio de classes personalizadas.</span><span class="sxs-lookup"><span data-stu-id="68b22-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="68b22-215">Por exemplo, um `<TableCell>` componente pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="68b22-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="68b22-216">No exemplo anterior, `Data` é diferente para cada célula, mas `Options` é comum em todos eles.</span><span class="sxs-lookup"><span data-stu-id="68b22-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="68b22-217">É claro que pode ser uma melhoria não ter um `<TableCell>` componente e, em vez disso, embutir sua lógica no componente pai.</span><span class="sxs-lookup"><span data-stu-id="68b22-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="68b22-218">Garantir que os parâmetros em cascata sejam corrigidos</span><span class="sxs-lookup"><span data-stu-id="68b22-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="68b22-219">O `<CascadingValue>` componente tem um parâmetro opcional chamado `IsFixed` .</span><span class="sxs-lookup"><span data-stu-id="68b22-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="68b22-220">Se o `IsFixed` valor for `false` (o padrão), cada destinatário do valor em cascata configurará uma assinatura para receber notificações de alteração.</span><span class="sxs-lookup"><span data-stu-id="68b22-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="68b22-221">Nesse caso, cada `[CascadingParameter]` um é **substancialmente mais caro** do que `[Parameter]` o normal devido ao acompanhamento da assinatura.</span><span class="sxs-lookup"><span data-stu-id="68b22-221">In this case, each each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="68b22-222">Se o `IsFixed` valor for `true` (por exemplo, `<CascadingValue Value="@someValue" IsFixed="true">` ), destinatários receberá o valor inicial, mas *não* configurará nenhuma assinatura para receber atualizações.</span><span class="sxs-lookup"><span data-stu-id="68b22-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="68b22-223">Nesse caso, cada `[CascadingParameter]` uma é leve e **não mais cara** do que uma normal `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="68b22-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="68b22-224">Sempre que possível, você deve usar `IsFixed="true"` em valores em cascata.</span><span class="sxs-lookup"><span data-stu-id="68b22-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="68b22-225">Você pode fazer isso sempre que o valor que está sendo fornecido não for alterado ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="68b22-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="68b22-226">No padrão comum em que um componente passa `this` como um valor em cascata, você deve usar `IsFixed="true"` :</span><span class="sxs-lookup"><span data-stu-id="68b22-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="68b22-227">Isso faz uma enorme diferença se houver um grande número de outros componentes que recebam o valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="68b22-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="68b22-228">Para obter mais informações, consulte <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="68b22-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="68b22-229">Evite o atributo nivelamento com `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="68b22-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="68b22-230">Os componentes podem optar por receber valores de parâmetro "não correspondentes" usando o <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> sinalizador:</span><span class="sxs-lookup"><span data-stu-id="68b22-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="68b22-231">Essa abordagem permite passar atributos adicionais arbitrários para o elemento.</span><span class="sxs-lookup"><span data-stu-id="68b22-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="68b22-232">No entanto, também é muito caro porque o renderizador deve:</span><span class="sxs-lookup"><span data-stu-id="68b22-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="68b22-233">Corresponder a todos os parâmetros fornecidos em relação ao conjunto de parâmetros conhecidos para criar um dicionário.</span><span class="sxs-lookup"><span data-stu-id="68b22-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="68b22-234">Mantenha o controle de como várias cópias do mesmo atributo substituem umas às outras.</span><span class="sxs-lookup"><span data-stu-id="68b22-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="68b22-235">Sinta-se à vontade para usar <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> em componentes sem desempenho crítico, como aqueles que não são repetidos com frequência.</span><span class="sxs-lookup"><span data-stu-id="68b22-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="68b22-236">No entanto, para componentes que são renderizados em escala, como cada item em uma grande lista ou células em uma grade, tente evitar o atributo nivelamento.</span><span class="sxs-lookup"><span data-stu-id="68b22-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="68b22-237">Para obter mais informações, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="68b22-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="68b22-238">Implementar `SetParametersAsync` manualmente</span><span class="sxs-lookup"><span data-stu-id="68b22-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="68b22-239">Um dos principais aspectos da sobrecarga de renderização por componente é gravar valores de parâmetro de entrada nas `[Parameter]` Propriedades.</span><span class="sxs-lookup"><span data-stu-id="68b22-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="68b22-240">O renderizador precisa usar a reflexão para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="68b22-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="68b22-241">Embora isso seja um pouco otimizado, a ausência de suporte JIT no tempo de execução do Webassembly impõe limites.</span><span class="sxs-lookup"><span data-stu-id="68b22-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="68b22-242">Em alguns casos extremos, talvez você queira evitar a reflexão e implementar sua própria lógica de configuração de parâmetro manualmente.</span><span class="sxs-lookup"><span data-stu-id="68b22-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="68b22-243">Isso pode ser aplicável quando:</span><span class="sxs-lookup"><span data-stu-id="68b22-243">This may be applicable when:</span></span>

 * <span data-ttu-id="68b22-244">Você tem um componente que renderiza extremamente frequentemente (por exemplo, há centenas ou milhares de cópias dele na interface do usuário).</span><span class="sxs-lookup"><span data-stu-id="68b22-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="68b22-245">Ele aceita muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="68b22-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="68b22-246">Você descobre que a sobrecarga dos parâmetros de recebimento tem um impacto observável na capacidade de resposta da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="68b22-247">Nesses casos, você pode substituir o método virtual do componente <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e implementar sua própria lógica específica do componente.</span><span class="sxs-lookup"><span data-stu-id="68b22-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="68b22-248">O exemplo a seguir evita deliberadamente quaisquer pesquisas de dicionário:</span><span class="sxs-lookup"><span data-stu-id="68b22-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="68b22-249">No código anterior, retornar a classe base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> executa os métodos de ciclo de vida normais sem atribuir parâmetros novamente.</span><span class="sxs-lookup"><span data-stu-id="68b22-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="68b22-250">Como você pode ver no código anterior, substituir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e fornecer lógica personalizada é complicado e trabalhoso, portanto, não recomendamos essa abordagem em geral.</span><span class="sxs-lookup"><span data-stu-id="68b22-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="68b22-251">Em casos extremos, ele pode melhorar o desempenho de renderização em 20-25%, mas você só deve considerar essa abordagem nos cenários listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="68b22-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="68b22-252">Não dispare eventos muito rapidamente</span><span class="sxs-lookup"><span data-stu-id="68b22-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="68b22-253">Alguns eventos de navegador são acionados com muita frequência, por exemplo, `onmousemove` e `onscroll` , que podem ser lançados dezenas ou centenas de vezes por segundo.</span><span class="sxs-lookup"><span data-stu-id="68b22-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="68b22-254">Na maioria dos casos, você não precisa executar atualizações da interface do usuário com frequência.</span><span class="sxs-lookup"><span data-stu-id="68b22-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="68b22-255">Se você tentar fazer isso, poderá prejudicar a capacidade de resposta da interface do usuário ou consumir tempo excessivo de CPU.</span><span class="sxs-lookup"><span data-stu-id="68b22-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="68b22-256">Em vez de usar `@onmousemove` `@onscroll` eventos nativos, você pode preferir usar a interoperabilidade js para registrar um retorno de chamada que é acionado com menos frequência.</span><span class="sxs-lookup"><span data-stu-id="68b22-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="68b22-257">Por exemplo, o componente a seguir ( `MyComponent.razor` ) exibe a posição do mouse, mas só atualiza no máximo uma vez a cada 500 MS:</span><span class="sxs-lookup"><span data-stu-id="68b22-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="68b22-258">O código JavaScript correspondente, que pode ser colocado na `index.html` página ou carregado como um módulo ES6, registra o ouvinte de eventos dom real.</span><span class="sxs-lookup"><span data-stu-id="68b22-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="68b22-259">Neste exemplo, o ouvinte de eventos usa a [ `throttle` função do Lodash](https://lodash.com/docs/4.17.15#throttle) para limitar a taxa de invocações:</span><span class="sxs-lookup"><span data-stu-id="68b22-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="68b22-260">Essa técnica pode ser ainda mais importante para :::no-loc(Blazor Server)::: o, já que cada invocação de evento envolve a entrega de uma mensagem pela rede.</span><span class="sxs-lookup"><span data-stu-id="68b22-260">This technique can be even more important for :::no-loc(Blazor Server):::, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="68b22-261">É valioso :::no-loc(Blazor WebAssembly)::: porque pode reduzir muito a quantidade de trabalho de renderização.</span><span class="sxs-lookup"><span data-stu-id="68b22-261">It's valuable for :::no-loc(Blazor WebAssembly)::: because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="68b22-262">Otimizar a velocidade de interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="68b22-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="68b22-263">Chamadas entre .NET e JavaScript envolvem alguma sobrecarga adicional porque:</span><span class="sxs-lookup"><span data-stu-id="68b22-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="68b22-264">Por padrão, as chamadas são assíncronas.</span><span class="sxs-lookup"><span data-stu-id="68b22-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="68b22-265">Por padrão, os parâmetros e os valores de retorno são serializados em JSON.</span><span class="sxs-lookup"><span data-stu-id="68b22-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="68b22-266">Isso é para fornecer um mecanismo de conversão fácil de entender entre os tipos .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="68b22-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="68b22-267">Além disso :::no-loc(Blazor Server)::: , essas chamadas são passadas pela rede.</span><span class="sxs-lookup"><span data-stu-id="68b22-267">Additionally on :::no-loc(Blazor Server):::, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="68b22-268">Evite chamadas refinadas excessivamente</span><span class="sxs-lookup"><span data-stu-id="68b22-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="68b22-269">Como cada chamada envolve alguma sobrecarga, pode ser importante reduzir o número de chamadas.</span><span class="sxs-lookup"><span data-stu-id="68b22-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="68b22-270">Considere o código a seguir, que armazena uma coleção de itens na loja do navegador `localStorage` :</span><span class="sxs-lookup"><span data-stu-id="68b22-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="68b22-271">O exemplo anterior faz uma chamada de interoperabilidade do JS separada para cada item.</span><span class="sxs-lookup"><span data-stu-id="68b22-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="68b22-272">Em vez disso, a seguinte abordagem reduz a interoperabilidade de JS para uma única chamada:</span><span class="sxs-lookup"><span data-stu-id="68b22-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="68b22-273">A função JavaScript correspondente definida da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="68b22-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="68b22-274">Para :::no-loc(Blazor WebAssembly)::: o, isso geralmente só é importante se você estiver fazendo um grande número de chamadas de interoperabilidade js.</span><span class="sxs-lookup"><span data-stu-id="68b22-274">For :::no-loc(Blazor WebAssembly):::, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="68b22-275">Considere fazer chamadas síncronas</span><span class="sxs-lookup"><span data-stu-id="68b22-275">Consider making synchronous calls</span></span>

<span data-ttu-id="68b22-276">As chamadas de interoperabilidade do JavaScript são assíncronas por padrão, independentemente de o código que está sendo chamado ser síncrono ou assíncrono.</span><span class="sxs-lookup"><span data-stu-id="68b22-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="68b22-277">Isso é para garantir que os componentes sejam compatíveis com o :::no-loc(Blazor WebAssembly)::: e o :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="68b22-277">This is to ensure components are compatible with both :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="68b22-278">Em :::no-loc(Blazor Server)::: , todas as chamadas de interoperabilidade do JavaScript devem ser assíncronas porque são enviadas por uma conexão de rede.</span><span class="sxs-lookup"><span data-stu-id="68b22-278">On :::no-loc(Blazor Server):::, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="68b22-279">Se você tiver certeza de que seu aplicativo só é executado :::no-loc(Blazor WebAssembly)::: , você pode optar por fazer chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="68b22-279">If you know for certain that your app only ever runs on :::no-loc(Blazor WebAssembly):::, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="68b22-280">Isso tem um pouco menos de sobrecarga do que fazer chamadas assíncronas e pode resultar em menos ciclos de processamento porque não há um estado intermediário ao aguardar os resultados.</span><span class="sxs-lookup"><span data-stu-id="68b22-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="68b22-281">Para fazer uma chamada síncrona do .NET para o JavaScript, converta <xref:Microsoft.JSInterop.IJSRuntime> em <xref:Microsoft.JSInterop.IJSInProcessRuntime> :</span><span class="sxs-lookup"><span data-stu-id="68b22-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="68b22-282">Ao trabalhar com `IJSObjectReference` o, você pode fazer uma chamada síncrona por meio da conversão para `IJSInProcessObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="68b22-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="68b22-283">Para fazer uma chamada síncrona do JavaScript para o .NET, use `DotNet.invokeMethod` em vez de `DotNet.invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="68b22-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="68b22-284">Chamadas síncronas funcionam se:</span><span class="sxs-lookup"><span data-stu-id="68b22-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="68b22-285">O aplicativo está sendo executado em :::no-loc(Blazor WebAssembly)::: , não :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="68b22-285">The app is running on :::no-loc(Blazor WebAssembly):::, not :::no-loc(Blazor Server):::.</span></span>
* <span data-ttu-id="68b22-286">A função chamada retorna um valor de forma síncrona (não é um `async` método e não retorna um .NET <xref:System.Threading.Tasks.Task> ou JavaScript `Promise` ).</span><span class="sxs-lookup"><span data-stu-id="68b22-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="68b22-287">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="68b22-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="68b22-288">Considere fazer chamadas não marshalled</span><span class="sxs-lookup"><span data-stu-id="68b22-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="68b22-289">Ao executar em :::no-loc(Blazor WebAssembly)::: , é possível fazer chamadas não Marshalling de .net para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="68b22-289">When running on :::no-loc(Blazor WebAssembly):::, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="68b22-290">Essas são chamadas síncronas que não executam a serialização JSON de argumentos ou valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="68b22-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="68b22-291">Todos os aspectos do gerenciamento de memória e as traduções entre as representações do .NET e do JavaScript são deixados para o desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="68b22-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="68b22-292">Embora o uso do `IJSUnmarshalledRuntime` tenha a menor sobrecarga das abordagens de interoperabilidade do js, as APIs de JavaScript necessárias para interagir com essas APIs não estão documentadas no momento e estão sujeitas a alterações significativas em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="68b22-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

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
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="68b22-293">Minimizar o tamanho do download do aplicativo</span><span class="sxs-lookup"><span data-stu-id="68b22-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="68b22-294">Corte de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="68b22-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="68b22-295">A [corte de assemblies não utilizados de um :::no-loc(Blazor WebAssembly)::: aplicativo](xref:blazor/host-and-deploy/configure-trimmer) reduz o tamanho do aplicativo removendo o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68b22-295">[Trimming unused assemblies from a :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="68b22-296">Por padrão, o corte é executado durante a publicação de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68b22-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="68b22-297">Para se beneficiar da remoção, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :</span><span class="sxs-lookup"><span data-stu-id="68b22-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="68b22-298">Vinculação de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="68b22-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="68b22-299">A [vinculação de um :::no-loc(Blazor WebAssembly)::: aplicativo](xref:blazor/host-and-deploy/configure-linker) reduz o tamanho do aplicativo ao cortar o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68b22-299">[Linking a :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="68b22-300">Por padrão, o vinculador de linguagem intermediária (IL) só é habilitado durante a criação da `Release` configuração.</span><span class="sxs-lookup"><span data-stu-id="68b22-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="68b22-301">Para se beneficiar disso, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :</span><span class="sxs-lookup"><span data-stu-id="68b22-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="68b22-302">Usar System.Text.Jsem</span><span class="sxs-lookup"><span data-stu-id="68b22-302">Use System.Text.Json</span></span>

<span data-ttu-id="68b22-303">:::no-loc(Blazor):::a implementação de interoperabilidade do JS depende de <xref:System.Text.Json> , que é uma biblioteca de SERIALIZAÇÃO JSON de alto desempenho com alocação de memória insuficiente.</span><span class="sxs-lookup"><span data-stu-id="68b22-303">:::no-loc(Blazor):::'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="68b22-304">O uso <xref:System.Text.Json> do não resulta em tamanho de carga de aplicativo adicional ao adicionar uma ou mais bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="68b22-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="68b22-305">Para obter diretrizes de migração, consulte [como migrar do `Newtonsoft.Json` para `System.Text.Json` o ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="68b22-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="68b22-306">Assemblies de carga lenta</span><span class="sxs-lookup"><span data-stu-id="68b22-306">Lazy load assemblies</span></span>

<span data-ttu-id="68b22-307">Carregar assemblies em tempo de execução quando os assemblies são exigidos por uma rota.</span><span class="sxs-lookup"><span data-stu-id="68b22-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="68b22-308">Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="68b22-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="68b22-309">Compactação</span><span class="sxs-lookup"><span data-stu-id="68b22-309">Compression</span></span>

<span data-ttu-id="68b22-310">Quando um :::no-loc(Blazor WebAssembly)::: aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="68b22-310">When a :::no-loc(Blazor WebAssembly)::: app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="68b22-311">:::no-loc(Blazor)::: depende do servidor para executar negotation de conteúdo e fornecer arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="68b22-311">:::no-loc(Blazor)::: relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="68b22-312">Depois que um aplicativo for implantado, verifique se o aplicativo serve arquivos compactados.</span><span class="sxs-lookup"><span data-stu-id="68b22-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="68b22-313">Inspecione a guia rede no Ferramentas para Desenvolvedores de um navegador e verifique se os arquivos são servidos com `Content-Encoding: br` ou `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="68b22-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="68b22-314">Se o host não estiver servindo arquivos compactados, siga as instruções em <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="68b22-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="68b22-315">Desabilitar recursos não utilizados</span><span class="sxs-lookup"><span data-stu-id="68b22-315">Disable unused features</span></span>

<span data-ttu-id="68b22-316">:::no-loc(Blazor WebAssembly):::o tempo de execução do inclui os seguintes recursos do .NET que podem ser desabilitados se o aplicativo não exigir um tamanho de carga menor:</span><span class="sxs-lookup"><span data-stu-id="68b22-316">:::no-loc(Blazor WebAssembly):::'s runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="68b22-317">Um arquivo de dados está incluído para tornar as informações de fuso horário corretas.</span><span class="sxs-lookup"><span data-stu-id="68b22-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="68b22-318">Se o aplicativo não exigir esse recurso, considere a possibilidade de desabilitá-lo definindo a `:::no-loc(Blazor):::EnableTimeZoneSupport` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="68b22-318">If the app doesn't require this feature, consider disabling it by setting the `:::no-loc(Blazor):::EnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <:::no-loc(Blazor):::EnableTimeZoneSupport>false</:::no-loc(Blazor):::EnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="68b22-319">Por padrão, :::no-loc(Blazor WebAssembly)::: o transporta os recursos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="68b22-319">By default, :::no-loc(Blazor WebAssembly)::: carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="68b22-320">Se o aplicativo não exigir localização, você poderá [Configurar o aplicativo para dar suporte à cultura invariável](xref:blazor/globalization-localization), que se baseia na `en-US` cultura:</span><span class="sxs-lookup"><span data-stu-id="68b22-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="68b22-321">As informações de agrupamento estão incluídas para fazer APIs como o <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionamento correto.</span><span class="sxs-lookup"><span data-stu-id="68b22-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="68b22-322">Se você tiver certeza de que o aplicativo não exige os dados de agrupamento, considere desabilitá-lo definindo a `:::no-loc(Blazor):::WebAssemblyPreserveCollationData` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="68b22-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `:::no-loc(Blazor):::WebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <:::no-loc(Blazor):::WebAssemblyPreserveCollationData>false</:::no-loc(Blazor):::WebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
