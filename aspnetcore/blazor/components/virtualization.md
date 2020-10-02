---
title: BlazorVirtualização de componente ASP.NET Core
author: guardrex
description: Saiba como usar a virtualização de componentes em Blazor aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653874"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualização de componente ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura. A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento. Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista de itens e apenas um subconjunto de itens é necessário para ser visível em um determinado momento. Blazor fornece o `Virtualize` componente que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.

::: moniker range=">= aspnetcore-5.0"

Sem a virtualização, uma lista típica pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item na lista:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo. O usuário pode experimentar um atraso de interface do usuário perceptível.

Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com `Items` . Somente os itens visíveis no momento são renderizados:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `@context.{PROPERTY}` ) no modelo de conteúdo do item:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

O `Virtualize` componente calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.

O conteúdo do item para o `Virtualize` componente pode incluir:

* HTML e código sem formatação Razor , como mostra o exemplo anterior.
* Um ou mais Razor componentes.
* Uma combinação de HTML/ Razor e Razor componentes.

## <a name="item-provider-delegate"></a>Representante do provedor de item

Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente `ItemsProvider` que recupera de forma assíncrona os itens solicitados sob demanda:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

O provedor de itens recebe um `ItemsProviderRequest` , que especifica o número necessário de itens começando em um índice de início específico. O provedor de itens recupera os itens solicitados de um banco de dados ou outro serviço e os retorna como um `ItemsProviderResult<TItem>` juntamente com uma contagem do total de itens. O provedor de itens pode optar por recuperar os itens com cada solicitação ou armazená-los em cache para que estejam prontamente disponíveis. Não tente usar um provedor de itens e atribua uma coleção para `Items` o mesmo `Virtualize` componente.

O exemplo a seguir carrega os funcionários de um `EmployeeService` :

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

## <a name="placeholder"></a>Espaço reservado

Como a solicitação de itens de uma fonte de dados remota pode levar algum tempo, você tem a opção de renderizar um espaço reservado ( `<Placeholder>...</Placeholder>` ) até que os dados do item estejam disponíveis:

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

## <a name="item-size"></a>Tamanho do item

O tamanho de cada item em pixels pode ser definido com `ItemSize` (padrão: 50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Contagem de sobreverificações

`OverscanCount` Determina quantos itens adicionais são renderizados antes e depois da região visível. Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem. No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Por exemplo, uma grade ou lista que renderiza centenas de linhas que contêm componentes tem uso intensivo de processador para renderização. Considere a possibilidade de virtualizar um layout de grade ou lista para que apenas um subconjunto dos componentes seja renderizado em um determinado momento.

O componente a seguir `Virtualize` ( `Virtualize.cs` ) implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para renderizar o conteúdo filho com base na rolagem do usuário:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

O componente a seguir `FetchData` ( `FetchData.razor` ) usa o `Virtualize` componente anterior para exibir 25 linhas de dados meteorológicos por vez:

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

No exemplo anterior, a abordagem é sobre como evitar o posicionamento absoluto de cada item individual. O posicionamento absoluto teria algumas vantagens (podemos ter certeza de que os itens ocupam a quantidade especificada de espaço em Y), mas que tem a desvantagem inadequada que você perde as larguras normais e não pode obter colunas de tabela para alinhar entre linhas/cabeçalho quando com base no dimensionamento de conteúdo.

O conceito por trás do design do `Virtualize` componente é que o componente não altera como os itens são dispostos dentro do dom. Não há nenhum elemento wrapper Adicionado, além do único cujo `TagName` especificado por você.

A melhor abordagem é evitar até mesmo o `TagName` elemento wrapper. Fazer com que o `Virtualize` componente não emita nenhum elemento próprio. Todo o componente é renderizado no entanto, muitas das instâncias de modelo são necessárias para preencher qualquer espaço visível restante no ancestral rolável mais próximo, além de adicionar um elemento de espaçador a seguir que torna o ancestral rolável com o intervalo de rolagem direita. No que diz respeito ao layout, é o mesmo que se a gama completa de filhos estivesse fisicamente no DOM. No entanto, ele exige que você especifique um modo preciso `ItemHeight` . Se você o receber errado (por exemplo, porque você está confuso e considerar que é válido especificar `style.height` em um `<tr>` ), o componente acabará renderizando o número incorreto de instâncias de modelo e não preencherá a interface do usuário ou renderizará de forma ineficiente demais. Além disso, o intervalo de rolagem no pai não estará correto.

::: moniker-end

## <a name="state-changes"></a>Alterações de estado

Ao fazer alterações em itens renderizados pelo `Virtualize` componente, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forçar reavaliação e reprocessamento do componente.
