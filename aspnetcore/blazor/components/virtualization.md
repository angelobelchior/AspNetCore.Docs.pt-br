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
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualização de componente ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura. A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento. Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista ou uma tabela com muitas linhas e apenas um subconjunto de itens é necessário para ser visível em um determinado momento. Blazor fornece o `Virtualize` componente que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.

::: moniker range=">= aspnetcore-5.0"

Sem a virtualização, uma lista típica ou um componente baseado em tabela pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item da lista ou cada linha na tabela:

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

Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo. O usuário pode experimentar um atraso de interface do usuário perceptível.

Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com `Items` . Somente os itens visíveis no momento são renderizados:

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

Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `@context.{PROPERTY}` ) no modelo de conteúdo do item:

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

O `Virtualize` componente calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.

## <a name="item-provider-delegate"></a>Representante do provedor de item

Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente `ItemsProvider` que recupera de forma assíncrona os itens solicitados sob demanda:

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

## <a name="item-size"></a>Tamanho do item

O tamanho de cada item em pixels pode ser definido com `ItemSize` (padrão: 50px):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a>Contagem de sobreverificações

`OverscanCount` Determina quantos itens adicionais são renderizados antes e depois da região visível. Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem. No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Por exemplo, uma grade ou lista que renderiza centenas de linhas que contêm componentes tem uso intensivo de processador para renderização. Considere a possibilidade de virtualizar um layout de grade ou lista para que apenas um subconjunto dos componentes seja renderizado em um determinado momento. Para obter um exemplo de renderização de subconjunto de componentes, consulte os seguintes componentes no [ `Virtualization` aplicativo de exemplo (repositório GitHub ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): um componente escrito em C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para renderizar um conjunto de linhas de dados meteorológicos com base na rolagem do usuário.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa o `Virtualize` componente para exibir 25 linhas de dados meteorológicos por vez.

::: moniker-end
