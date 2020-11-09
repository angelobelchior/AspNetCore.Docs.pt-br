---
title: BlazorVirtualização de componente ASP.NET Core
author: guardrex
description: 'Saiba como usar a virtualização de componentes em Blazor aplicativos ASP.NET Core.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: b23e4814daaabbe2c8660d49cc5b6940a9cc3b4f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056160"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="c2a24-103">BlazorVirtualização de componente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2a24-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="c2a24-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c2a24-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c2a24-105">Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura.</span><span class="sxs-lookup"><span data-stu-id="c2a24-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="c2a24-106">A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="c2a24-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="c2a24-107">Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista de itens e apenas um subconjunto de itens é necessário para ser visível em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="c2a24-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="c2a24-108">Blazor fornece o `Virtualize` componente que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c2a24-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="c2a24-109">Sem a virtualização, uma lista típica pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item na lista:</span><span class="sxs-lookup"><span data-stu-id="c2a24-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="c2a24-110">Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo.</span><span class="sxs-lookup"><span data-stu-id="c2a24-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="c2a24-111">O usuário pode experimentar um atraso de interface do usuário perceptível.</span><span class="sxs-lookup"><span data-stu-id="c2a24-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="c2a24-112">Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com `Items` .</span><span class="sxs-lookup"><span data-stu-id="c2a24-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="c2a24-113">Somente os itens visíveis no momento são renderizados:</span><span class="sxs-lookup"><span data-stu-id="c2a24-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="c2a24-114">Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `@context.{PROPERTY}` ) no modelo de conteúdo do item:</span><span class="sxs-lookup"><span data-stu-id="c2a24-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="c2a24-115">O `Virtualize` componente calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.</span><span class="sxs-lookup"><span data-stu-id="c2a24-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="c2a24-116">O conteúdo do item para o `Virtualize` componente pode incluir:</span><span class="sxs-lookup"><span data-stu-id="c2a24-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="c2a24-117">HTML e código sem formatação Razor , como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="c2a24-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="c2a24-118">Um ou mais Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="c2a24-118">One or more Razor components.</span></span>
* <span data-ttu-id="c2a24-119">Uma combinação de HTML/ Razor e Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="c2a24-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="c2a24-120">Representante do provedor de item</span><span class="sxs-lookup"><span data-stu-id="c2a24-120">Item provider delegate</span></span>

<span data-ttu-id="c2a24-121">Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente `ItemsProvider` que recupera de forma assíncrona os itens solicitados sob demanda:</span><span class="sxs-lookup"><span data-stu-id="c2a24-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="c2a24-122">O provedor de itens recebe um `ItemsProviderRequest` , que especifica o número necessário de itens começando em um índice de início específico.</span><span class="sxs-lookup"><span data-stu-id="c2a24-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="c2a24-123">O provedor de itens recupera os itens solicitados de um banco de dados ou outro serviço e os retorna como um `ItemsProviderResult<TItem>` juntamente com uma contagem do total de itens.</span><span class="sxs-lookup"><span data-stu-id="c2a24-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="c2a24-124">O provedor de itens pode optar por recuperar os itens com cada solicitação ou armazená-los em cache para que estejam prontamente disponíveis.</span><span class="sxs-lookup"><span data-stu-id="c2a24-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="c2a24-125">Não tente usar um provedor de itens e atribua uma coleção para `Items` o mesmo `Virtualize` componente.</span><span class="sxs-lookup"><span data-stu-id="c2a24-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="c2a24-126">O exemplo a seguir carrega os funcionários de um `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="c2a24-126">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a><span data-ttu-id="c2a24-127">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="c2a24-127">Placeholder</span></span>

<span data-ttu-id="c2a24-128">Como a solicitação de itens de uma fonte de dados remota pode levar algum tempo, você tem a opção de renderizar um espaço reservado ( `<Placeholder>...</Placeholder>` ) até que os dados do item estejam disponíveis:</span><span class="sxs-lookup"><span data-stu-id="c2a24-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="c2a24-129">Tamanho do item</span><span class="sxs-lookup"><span data-stu-id="c2a24-129">Item size</span></span>

<span data-ttu-id="c2a24-130">O tamanho de cada item em pixels pode ser definido com `ItemSize` (padrão: 50px):</span><span class="sxs-lookup"><span data-stu-id="c2a24-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="c2a24-131">Contagem de sobreverificações</span><span class="sxs-lookup"><span data-stu-id="c2a24-131">Overscan count</span></span>

<span data-ttu-id="c2a24-132">`OverscanCount` Determina quantos itens adicionais são renderizados antes e depois da região visível.</span><span class="sxs-lookup"><span data-stu-id="c2a24-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="c2a24-133">Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem.</span><span class="sxs-lookup"><span data-stu-id="c2a24-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="c2a24-134">No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):</span><span class="sxs-lookup"><span data-stu-id="c2a24-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="c2a24-135">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="c2a24-135">State changes</span></span>

<span data-ttu-id="c2a24-136">Ao fazer alterações em itens renderizados pelo `Virtualize` componente, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forçar reavaliação e reprocessamento do componente.</span><span class="sxs-lookup"><span data-stu-id="c2a24-136">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
