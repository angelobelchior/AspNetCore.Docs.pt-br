---
title: ASP.NET Core Blazor componentes modelados
author: guardrex
description: Saiba como os componentes modelados podem aceitar um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: 74601905b7317ad8d9763fe0d747ba36bd0b1389
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393789"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="518a2-103">ASP.NET Core Blazor componentes modelados</span><span class="sxs-lookup"><span data-stu-id="518a2-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="518a2-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="518a2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="518a2-105">Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="518a2-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="518a2-106">Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais.</span><span class="sxs-lookup"><span data-stu-id="518a2-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="518a2-107">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="518a2-107">A couple of examples include:</span></span>

* <span data-ttu-id="518a2-108">Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="518a2-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="518a2-109">Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="518a2-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="518a2-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="518a2-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="518a2-111">Parâmetros de modelo</span><span class="sxs-lookup"><span data-stu-id="518a2-111">Template parameters</span></span>

<span data-ttu-id="518a2-112">Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo <xref:Microsoft.AspNetCore.Components.RenderFragment> ou <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="518a2-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="518a2-113">Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="518a2-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="518a2-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="518a2-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="518a2-115">`TableTemplate` componente ( `TableTemplate.razor` ):</span><span class="sxs-lookup"><span data-stu-id="518a2-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="518a2-116">Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros ( `TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="518a2-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="518a2-117">As restrições de tipo genérico terão suporte em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="518a2-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="518a2-118">Para obter mais informações, consulte [permitir restrições de tipo genérico (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="518a2-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="518a2-119">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="518a2-119">Template context parameters</span></span>

<span data-ttu-id="518a2-120">Argumentos de componente do tipo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passado como elementos têm um parâmetro implícito chamado `context` (por exemplo, do exemplo de código anterior, `@context.PetId` ), mas você pode alterar o nome do parâmetro usando o `Context` atributo no elemento filho.</span><span class="sxs-lookup"><span data-stu-id="518a2-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="518a2-121">No exemplo a seguir, o `RowTemplate` atributo do elemento `Context` especifica o `pet` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="518a2-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

<span data-ttu-id="518a2-122">Como alternativa, você pode especificar o `Context` atributo no elemento Component.</span><span class="sxs-lookup"><span data-stu-id="518a2-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="518a2-123">O `Context` atributo especificado se aplica a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="518a2-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="518a2-124">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição).</span><span class="sxs-lookup"><span data-stu-id="518a2-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="518a2-125">No exemplo a seguir, o `Context` atributo aparece no `TableTemplate` elemento e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="518a2-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="518a2-126">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="518a2-126">Generic-typed components</span></span>

<span data-ttu-id="518a2-127">Os componentes modelo são geralmente digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="518a2-127">Templated components are often generically typed.</span></span> <span data-ttu-id="518a2-128">Por exemplo, um `ListViewTemplate` componente genérico ( `ListViewTemplate.razor` ) pode ser usado para renderizar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="518a2-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="518a2-129">Para definir um componente genérico, use a [`@typeparam`](xref:mvc/views/razor#typeparam) diretiva para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="518a2-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="518a2-130">Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="518a2-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="518a2-131">Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="518a2-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="518a2-132">No exemplo a seguir, `TItem="Pet"` especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="518a2-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
