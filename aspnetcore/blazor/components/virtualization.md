---
title: BlazorVirtualização de componente ASP.NET Core
author: guardrex
description: Saiba como usar a virtualização de componentes em Blazor aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/16/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: c904a3f330531f80d1dc69bf63621c59790e1cb0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722967"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="3a0f7-103">BlazorVirtualização de componente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a0f7-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="3a0f7-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3a0f7-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3a0f7-105">Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="3a0f7-106">A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="3a0f7-107">Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista ou uma tabela com muitas linhas e apenas um subconjunto de itens é necessário para ser visível em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="3a0f7-108">Blazor fornece o `Virtualize` componente que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3a0f7-109">Sem a virtualização, uma lista típica ou um componente baseado em tabela pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item da lista ou cada linha na tabela:</span><span class="sxs-lookup"><span data-stu-id="3a0f7-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="3a0f7-110">Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="3a0f7-111">O usuário pode experimentar um atraso de interface do usuário perceptível.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="3a0f7-112">Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com `Items` .</span><span class="sxs-lookup"><span data-stu-id="3a0f7-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="3a0f7-113">Somente os itens visíveis no momento são renderizados:</span><span class="sxs-lookup"><span data-stu-id="3a0f7-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="3a0f7-114">Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `@context.{PROPERTY}` ) no modelo de conteúdo do item:</span><span class="sxs-lookup"><span data-stu-id="3a0f7-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="3a0f7-115">O `Virtualize` componente calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="3a0f7-116">Representante do provedor de item</span><span class="sxs-lookup"><span data-stu-id="3a0f7-116">Item provider delegate</span></span>

<span data-ttu-id="3a0f7-117">Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente `ItemsProvider` que recupera de forma assíncrona os itens solicitados sob demanda:</span><span class="sxs-lookup"><span data-stu-id="3a0f7-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="3a0f7-118">O provedor de itens recebe um `ItemsProviderRequest` , que especifica o número necessário de itens começando em um índice de início específico.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="3a0f7-119">O provedor de itens recupera os itens solicitados de um banco de dados ou outro serviço e os retorna como um `ItemsProviderResult<TItem>` juntamente com uma contagem do total de itens.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="3a0f7-120">O provedor de itens pode optar por recuperar os itens com cada solicitação ou armazená-los em cache para que estejam prontamente disponíveis.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="3a0f7-121">Não tente usar um provedor de itens e atribua uma coleção para `Items` o mesmo `Virtualize` componente.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="3a0f7-122">O exemplo a seguir carrega os funcionários de um `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="3a0f7-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="3a0f7-123">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="3a0f7-123">Placeholder</span></span>

<span data-ttu-id="3a0f7-124">Como a solicitação de itens de uma fonte de dados remota pode levar algum tempo, você tem a opção de renderizar um espaço reservado ( `<Placeholder>...</Placeholder>` ) até que os dados do item estejam disponíveis:</span><span class="sxs-lookup"><span data-stu-id="3a0f7-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="3a0f7-125">Tamanho do item</span><span class="sxs-lookup"><span data-stu-id="3a0f7-125">Item size</span></span>

<span data-ttu-id="3a0f7-126">O tamanho de cada item em pixels pode ser definido com `ItemSize` (padrão: 50px):</span><span class="sxs-lookup"><span data-stu-id="3a0f7-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="3a0f7-127">Contagem de sobreverificações</span><span class="sxs-lookup"><span data-stu-id="3a0f7-127">Overscan count</span></span>

<span data-ttu-id="3a0f7-128">`OverscanCount` Determina quantos itens adicionais são renderizados antes e depois da região visível.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="3a0f7-129">Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="3a0f7-130">No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):</span><span class="sxs-lookup"><span data-stu-id="3a0f7-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3a0f7-131">Por exemplo, uma grade ou lista que renderiza centenas de linhas que contêm componentes tem uso intensivo de processador para renderização.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="3a0f7-132">Considere a possibilidade de virtualizar um layout de grade ou lista para que apenas um subconjunto dos componentes seja renderizado em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="3a0f7-133">Para obter um exemplo de renderização de subconjunto de componentes, consulte os seguintes componentes no [ `Virtualization` aplicativo de exemplo (repositório GitHub ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="3a0f7-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="3a0f7-134">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): um componente escrito em C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para renderizar um conjunto de linhas de dados meteorológicos com base na rolagem do usuário.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="3a0f7-135">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa o `Virtualize` componente para exibir 25 linhas de dados meteorológicos por vez.</span><span class="sxs-lookup"><span data-stu-id="3a0f7-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end
