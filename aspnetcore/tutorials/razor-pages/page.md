---
title: 'Parte 3, páginas do com Scaffold :::no-loc(Razor):::'
author: rick-anderson
description: 'Parte 3 da série de tutoriais em :::no-loc(Razor)::: páginas.'
ms.author: riande
ms.date: 09/25/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
- ':::no-loc(appsettings.json):::'
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a9494feacbe783b20a9f5eb98ef9e481f2c713fa
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360886"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="9045b-103">Parte 3, com Scaffold :::no-loc(Razor)::: páginas em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9045b-103">Part 3, scaffolded :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="9045b-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9045b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9045b-105">Este tutorial examina as :::no-loc(Razor)::: páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="9045b-105">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9045b-106">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9045b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="9045b-107">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9045b-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="9045b-108">As :::no-loc(Create)::: :::no-loc(Delete)::: páginas,, detalhes e editar</span><span class="sxs-lookup"><span data-stu-id="9045b-108">The :::no-loc(Create):::, :::no-loc(Delete):::, Details, and Edit pages</span></span>

<span data-ttu-id="9045b-109">Examine o modelo de página *páginas/filmes/ :::no-loc(Index)::: . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="9045b-109">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml.cs)]

<span data-ttu-id="9045b-110">:::no-loc(Razor)::: As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="9045b-110">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="9045b-111">Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="9045b-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="9045b-112">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `:::no-loc(Razor):::PagesMovieContext` à página:</span><span class="sxs-lookup"><span data-stu-id="9045b-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="9045b-113">Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9045b-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="9045b-114">Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-115">Em uma :::no-loc(Razor)::: página, `OnGetAsync` ou `OnGet` é chamado para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="9045b-115">On a :::no-loc(Razor)::: Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="9045b-116">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="9045b-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="9045b-117">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task` , nenhuma instrução de retorno é usada.</span><span class="sxs-lookup"><span data-stu-id="9045b-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="9045b-118">Por exemplo, a página de privacidade:</span><span class="sxs-lookup"><span data-stu-id="9045b-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="9045b-119">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="9045b-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="9045b-120">Por exemplo, o método *pages/Movies/ :::no-loc(Create)::: . cshtml.cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="9045b-120">For example, the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="9045b-121">Examine a página *páginas/filmes/ :::no-loc(Index)::: . cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="9045b-121">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml)]

<span data-ttu-id="9045b-122">:::no-loc(Razor)::: pode fazer a transição de HTML para C# ou para uma :::no-loc(Razor)::: marcação específica.</span><span class="sxs-lookup"><span data-stu-id="9045b-122">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="9045b-123">Quando um `@` símbolo é seguido por uma [ :::no-loc(Razor)::: palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma :::no-loc(Razor)::: marcação específica, caso contrário, ele faz a transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="9045b-123">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="9045b-124">A diretiva @page</span><span class="sxs-lookup"><span data-stu-id="9045b-124">The @page directive</span></span>

<span data-ttu-id="9045b-125">A `@page` :::no-loc(Razor)::: diretiva torna o arquivo uma ação MVC, o que significa que ele pode lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="9045b-125">The `@page` :::no-loc(Razor)::: directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="9045b-126">`@page` deve ser a primeira :::no-loc(Razor)::: diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="9045b-126">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="9045b-127">`@page` e `@model` são exemplos de transição para :::no-loc(Razor)::: marcação específica.</span><span class="sxs-lookup"><span data-stu-id="9045b-127">`@page` and `@model` are examples of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="9045b-128">Consulte a [ :::no-loc(Razor)::: sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-128">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="9045b-129">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="9045b-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="9045b-130">A `@model` diretiva especifica o tipo do modelo passado para a :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-130">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-131">No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-132">O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="9045b-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="9045b-133">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="9045b-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="9045b-134">O auxiliar HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="9045b-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="9045b-135">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="9045b-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="9045b-136">Isso significa que não há nenhuma violação de acesso quando `model` , `model.Movie` ou `model.Movie[0]` está `null` ou vazio.</span><span class="sxs-lookup"><span data-stu-id="9045b-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="9045b-137">Quando a expressão lambda é avaliada, por exemplo, com `@Html.DisplayFor(modelItem => item.Title)` , os valores de Propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="9045b-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="9045b-138">A página de layout</span><span class="sxs-lookup"><span data-stu-id="9045b-138">The layout page</span></span>

<span data-ttu-id="9045b-139">Selecione os links de menu **:::no-loc(Razor)::: PagesMovie** , **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="9045b-139">Select the menu links **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy**.</span></span> <span data-ttu-id="9045b-140">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="9045b-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="9045b-141">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="9045b-142">Abra e examine o arquivo *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9045b-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="9045b-143">Os modelos de [layout](xref:mvc/views/layout) permitem que o layout do contêiner HTML seja:</span><span class="sxs-lookup"><span data-stu-id="9045b-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="9045b-144">Especificado em um único lugar.</span><span class="sxs-lookup"><span data-stu-id="9045b-144">Specified in one place.</span></span>
* <span data-ttu-id="9045b-145">Aplicado a várias páginas no site.</span><span class="sxs-lookup"><span data-stu-id="9045b-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="9045b-146">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="9045b-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="9045b-147">`RenderBody` é um espaço reservado em que todas as exibições específicas da página são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="9045b-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="9045b-148">Por exemplo, selecione o link **Privacidade** e a exibição *Pages/Privacy.cshtml* será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="9045b-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="9045b-149">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="9045b-149">ViewData and layout</span></span>

<span data-ttu-id="9045b-150">Considere a seguinte marcação do arquivo *pages/Movies/ :::no-loc(Index)::: . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9045b-150">Consider the following markup from the *Pages/Movies/:::no-loc(Index):::.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="9045b-151">A marcação realçada anterior é um exemplo de :::no-loc(Razor)::: transição para C#.</span><span class="sxs-lookup"><span data-stu-id="9045b-151">The preceding highlighted markup is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="9045b-152">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="9045b-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="9045b-153">A `PageModel` classe base contém uma `ViewData` Propriedade Dictionary que pode ser usada para passar dados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="9045b-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="9045b-154">Os objetos são adicionados ao `ViewData` dicionário usando um padrão \* **valor de chave** _.</span><span class="sxs-lookup"><span data-stu-id="9045b-154">Objects are added to the `ViewData` dictionary using a \* **key value** _ pattern.</span></span> <span data-ttu-id="9045b-155">No exemplo anterior, a propriedade `Title` é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="9045b-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="9045b-156">A `Title` propriedade é usada no arquivo _Pages/shared/_Layout. cshtml \*.</span><span class="sxs-lookup"><span data-stu-id="9045b-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="9045b-157">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="9045b-158">A linha `@*Markup removed for brevity.*@` é um :::no-loc(Razor)::: comentário.</span><span class="sxs-lookup"><span data-stu-id="9045b-158">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="9045b-159">Ao contrário dos comentários HTML `<!-- -->` , :::no-loc(Razor)::: os comentários não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="9045b-159">Unlike HTML comments `<!-- -->`, :::no-loc(Razor)::: comments are not sent to the client.</span></span> <span data-ttu-id="9045b-160">Consulte [MDN Web docs: introdução ao HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="9045b-161">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="9045b-161">Update the layout</span></span>

1. <span data-ttu-id="9045b-162">Altere o `<title>` elemento no arquivo *pages/Shared/_Layout. cshtml* para exibir o **filme** em vez de **:::no-loc(Razor)::: PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="9045b-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="9045b-163">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/:::no-loc(Index):::">:::no-loc(Razor):::PagesMovie</a>
   ```

1. <span data-ttu-id="9045b-164">Substitua o elemento anterior pela marcação a seguir:</span><span class="sxs-lookup"><span data-stu-id="9045b-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/:::no-loc(Index):::">RpMovie</a>
   ```

   <span data-ttu-id="9045b-165">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="9045b-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="9045b-166">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9045b-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="9045b-167">O `asp-page="/Movies/:::no-loc(Index):::"` atributo e o valor da marca auxiliar cria um link para a `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-167">The `asp-page="/Movies/:::no-loc(Index):::"` Tag Helper attribute and value creates a link to the `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-168">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="9045b-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="9045b-169">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="9045b-170">Salve as alterações e teste o aplicativo selecionando o link **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="9045b-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="9045b-171">Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="9045b-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="9045b-172">Teste os links **Home** , **RpMovie** , **:::no-loc(Create):::** , **Edit** e **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="9045b-172">Test the **Home** , **RpMovie** , **:::no-loc(Create):::** , **Edit** , and **:::no-loc(Delete):::** links.</span></span> <span data-ttu-id="9045b-173">Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="9045b-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="9045b-174">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="9045b-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9045b-175">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades não inglesas que usam uma vírgula (",") para um ponto decimal e não US-English formatos de data, você deve executar etapas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9045b-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="9045b-176">Confira [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="9045b-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="9045b-177">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9045b-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="9045b-178">A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os :::no-loc(Razor)::: arquivos na pasta *pages* .</span><span class="sxs-lookup"><span data-stu-id="9045b-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="9045b-179">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="9045b-180">O :::no-loc(Create)::: modelo de página</span><span class="sxs-lookup"><span data-stu-id="9045b-180">The :::no-loc(Create)::: page model</span></span>

<span data-ttu-id="9045b-181">Examine o modelo de página *páginas/filmes/ :::no-loc(Create)::: . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="9045b-181">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetALL)]

<span data-ttu-id="9045b-182">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="9045b-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="9045b-183">A :::no-loc(Create)::: página não tem nenhum estado para inicializar; portanto, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="9045b-183">The :::no-loc(Create)::: page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="9045b-184">Apresentamos um exemplo de inicialização de estado `OnGet` posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="9045b-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="9045b-185">O `Page` método cria um `PageResult` objeto que renderiza a página *:::no-loc(Create)::: . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9045b-185">The `Page` method creates a `PageResult` object that renders the *:::no-loc(Create):::.cshtml* page.</span></span>

<span data-ttu-id="9045b-186">A `Movie` propriedade usa o atributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para aceitar a associação de [modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="9045b-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="9045b-187">Quando o :::no-loc(Create)::: formulário posta os valores de formulário, o tempo de execução de ASP.NET Core associa os valores postados ao `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="9045b-187">When the :::no-loc(Create)::: form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="9045b-188">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="9045b-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetPost)]

<span data-ttu-id="9045b-189">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="9045b-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="9045b-190">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="9045b-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="9045b-191">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="9045b-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="9045b-192">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="9045b-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="9045b-193">Se não houver nenhum erro de modelo:</span><span class="sxs-lookup"><span data-stu-id="9045b-193">If there are no model errors:</span></span>

* <span data-ttu-id="9045b-194">Os dados são salvos.</span><span class="sxs-lookup"><span data-stu-id="9045b-194">The data is saved.</span></span>
* <span data-ttu-id="9045b-195">O navegador é redirecionado para a :::no-loc(Index)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-195">The browser is redirected to the :::no-loc(Index)::: page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="9045b-196">A :::no-loc(Create)::: :::no-loc(Razor)::: página</span><span class="sxs-lookup"><span data-stu-id="9045b-196">The :::no-loc(Create)::: :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="9045b-197">Examine o arquivo de página *páginas/filmes/ :::no-loc(Create)::: . cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="9045b-197">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="9045b-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9045b-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9045b-199">O Visual Studio exibe as marcas a seguir em uma fonte em negrito diferente usada em auxiliares de marcações:</span><span class="sxs-lookup"><span data-stu-id="9045b-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Exibição de VS17 da página::: no-Loc (Create):::. cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9045b-201">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9045b-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9045b-202">Os auxiliares de marcações a seguir são exibidos na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="9045b-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="9045b-203">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9045b-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="9045b-204">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="9045b-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="9045b-205">O mecanismo scaffolding cria a :::no-loc(Razor)::: marcação para cada campo no modelo, exceto o ID, semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="9045b-205">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml?range=15-20)]

<span data-ttu-id="9045b-206">Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="9045b-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="9045b-207">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="9045b-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="9045b-208">A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `[for]` atributo para a `Title` propriedade.</span><span class="sxs-lookup"><span data-stu-id="9045b-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="9045b-209">O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="9045b-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="9045b-210">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="9045b-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9045b-211">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9045b-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9045b-212">[Anterior: adicionar um modelo](xref:tutorials/razor-pages/model) 
>  [Em seguida: trabalhar com um banco de dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="9045b-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9045b-213">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9045b-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9045b-214">Este tutorial examina as :::no-loc(Razor)::: páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="9045b-214">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="9045b-215">[Exiba ou baixe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) a amostra.</span><span class="sxs-lookup"><span data-stu-id="9045b-215">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) sample.</span></span>

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="9045b-216">As :::no-loc(Create)::: :::no-loc(Delete)::: páginas,, detalhes e editar</span><span class="sxs-lookup"><span data-stu-id="9045b-216">The :::no-loc(Create):::, :::no-loc(Delete):::, Details, and Edit pages</span></span>

<span data-ttu-id="9045b-217">Examine o modelo de página *páginas/filmes/ :::no-loc(Index)::: . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="9045b-217">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml.cs)]

<span data-ttu-id="9045b-218">:::no-loc(Razor)::: As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="9045b-218">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="9045b-219">Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="9045b-219">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="9045b-220">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `:::no-loc(Razor):::PagesMovieContext` à página.</span><span class="sxs-lookup"><span data-stu-id="9045b-220">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="9045b-221">As páginas com Scaffold seguem esse padrão.</span><span class="sxs-lookup"><span data-stu-id="9045b-221">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="9045b-222">Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9045b-222">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="9045b-223">Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-223">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-224">`OnGetAsync` ou `OnGet` é chamado em uma :::no-loc(Razor)::: página para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="9045b-224">`OnGetAsync` or `OnGet` is called on a :::no-loc(Razor)::: Page to initialize the state for the page.</span></span> <span data-ttu-id="9045b-225">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="9045b-225">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="9045b-226">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task` , nenhum método de retorno é usado.</span><span class="sxs-lookup"><span data-stu-id="9045b-226">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="9045b-227">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="9045b-227">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="9045b-228">Por exemplo, o método *pages/Movies/ :::no-loc(Create)::: . cshtml.cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="9045b-228">For example, the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="9045b-229">Examine a página *páginas/filmes/ :::no-loc(Index)::: . cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="9045b-229">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml)]

<span data-ttu-id="9045b-230">:::no-loc(Razor)::: pode fazer a transição de HTML para C# ou para uma :::no-loc(Razor)::: marcação específica.</span><span class="sxs-lookup"><span data-stu-id="9045b-230">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="9045b-231">Quando um `@` símbolo é seguido por uma [ :::no-loc(Razor)::: palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma :::no-loc(Razor)::: marcação específica, caso contrário, ele faz a transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="9045b-231">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="9045b-232">A `@page` :::no-loc(Razor)::: diretiva transforma o arquivo em uma ação MVC, o que significa que ele pode lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="9045b-232">The `@page` :::no-loc(Razor)::: directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="9045b-233">`@page` deve ser a primeira :::no-loc(Razor)::: diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="9045b-233">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="9045b-234">`@page` é um exemplo de transição para :::no-loc(Razor)::: marcação específica.</span><span class="sxs-lookup"><span data-stu-id="9045b-234">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="9045b-235">Consulte a [ :::no-loc(Razor)::: sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-235">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="9045b-236">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="9045b-236">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="9045b-237">A `@model` diretiva especifica o tipo do modelo passado para a :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-237">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-238">No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-238">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-239">O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="9045b-239">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="9045b-240">Auxiliares HTML</span><span class="sxs-lookup"><span data-stu-id="9045b-240">HTML Helpers</span></span>

<span data-ttu-id="9045b-241">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="9045b-241">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="9045b-242">O auxiliar HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="9045b-242">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="9045b-243">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="9045b-243">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="9045b-244">Isso significa que não há nenhuma violação de acesso quando `model`, `model.Movie` ou `model.Movie[0]` são `null` ou vazios.</span><span class="sxs-lookup"><span data-stu-id="9045b-244">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="9045b-245">Quando a expressão lambda é avaliada, por exemplo, com `@Html.DisplayFor(modelItem => item.Title)` , os valores de Propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="9045b-245">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="9045b-246">A página de layout</span><span class="sxs-lookup"><span data-stu-id="9045b-246">The layout page</span></span>

<span data-ttu-id="9045b-247">Selecione os links de menu **:::no-loc(Razor)::: PagesMovie** , **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="9045b-247">Select the menu links **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy**.</span></span> <span data-ttu-id="9045b-248">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="9045b-248">Each page shows the same menu layout.</span></span> <span data-ttu-id="9045b-249">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-249">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="9045b-250">Abra e examine o arquivo *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9045b-250">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="9045b-251">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML de um site em um único local e, em seguida, aplicá-lo em várias páginas no site.</span><span class="sxs-lookup"><span data-stu-id="9045b-251">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="9045b-252">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="9045b-252">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="9045b-253">`RenderBody` é um espaço reservado em que todas as visualizações específicas da página criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="9045b-253">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="9045b-254">Por exemplo, se você selecionar o link **Privacidade** , a exibição **Pages/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="9045b-254">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="9045b-255">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="9045b-255">ViewData and layout</span></span>

<span data-ttu-id="9045b-256">Considere o seguinte código do arquivo *pages/Movies/ :::no-loc(Index)::: . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9045b-256">Consider the following code from the *Pages/Movies/:::no-loc(Index):::.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="9045b-257">O código realçado anterior é um exemplo de :::no-loc(Razor)::: transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="9045b-257">The preceding highlighted code is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="9045b-258">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="9045b-258">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="9045b-259">A classe base `PageModel` tem uma propriedade de dicionário `ViewData` que pode ser usada para adicionar os dados que você deseja passar para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="9045b-259">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="9045b-260">Você adiciona objetos ao dicionário `ViewData` usando um padrão de chave/valor.</span><span class="sxs-lookup"><span data-stu-id="9045b-260">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="9045b-261">No exemplo anterior, a propriedade `Title` é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="9045b-261">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="9045b-262">A propriedade `Title` é usada no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-262">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="9045b-263">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-263">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="9045b-264">A linha `@*Markup removed for brevity.*@` é um :::no-loc(Razor)::: comentário.</span><span class="sxs-lookup"><span data-stu-id="9045b-264">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="9045b-265">Ao contrário dos comentários HTML `<!-- -->` , :::no-loc(Razor)::: os comentários não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="9045b-265">Unlike HTML comments `<!-- -->`, :::no-loc(Razor)::: comments are not sent to the client.</span></span> <span data-ttu-id="9045b-266">Consulte [MDN Web docs: introdução ao HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-266">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="9045b-267">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="9045b-267">Update the layout</span></span>

<span data-ttu-id="9045b-268">Altere o `<title>` elemento no arquivo *pages/Shared/_Layout. cshtml* para exibir o **filme** em vez de **:::no-loc(Razor)::: PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="9045b-268">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="9045b-269">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9045b-269">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/:::no-loc(Index):::">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="9045b-270">Substitua o elemento anterior pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="9045b-270">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/:::no-loc(Index):::">RpMovie</a>
```

<span data-ttu-id="9045b-271">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="9045b-271">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="9045b-272">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9045b-272">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="9045b-273">O `asp-page="/Movies/:::no-loc(Index):::"` atributo e o valor da marca auxiliar cria um link para a `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-273">The `asp-page="/Movies/:::no-loc(Index):::"` Tag Helper attribute and value creates a link to the `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="9045b-274">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="9045b-274">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="9045b-275">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-275">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="9045b-276">Salve suas alterações e teste o aplicativo clicando no link **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="9045b-276">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="9045b-277">Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="9045b-277">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="9045b-278">Teste os outros links ( **Home** , **RpMovie** , **:::no-loc(Create):::** , **Edit** e **:::no-loc(Delete):::** ).</span><span class="sxs-lookup"><span data-stu-id="9045b-278">Test the other links ( **Home** , **RpMovie** , **:::no-loc(Create):::** , **Edit** , and **:::no-loc(Delete):::** ).</span></span> <span data-ttu-id="9045b-279">Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="9045b-279">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="9045b-280">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="9045b-280">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9045b-281">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades não inglesas que usam uma vírgula (",") para um ponto decimal e não US-English formatos de data, você deve executar etapas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9045b-281">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="9045b-282">Veja [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="9045b-282">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="9045b-283">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9045b-283">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="9045b-284">A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os :::no-loc(Razor)::: arquivos na pasta *pages* .</span><span class="sxs-lookup"><span data-stu-id="9045b-284">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="9045b-285">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9045b-285">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="9045b-286">O :::no-loc(Create)::: modelo de página</span><span class="sxs-lookup"><span data-stu-id="9045b-286">The :::no-loc(Create)::: page model</span></span>

<span data-ttu-id="9045b-287">Examine o modelo de página *páginas/filmes/ :::no-loc(Create)::: . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="9045b-287">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetALL)]

<span data-ttu-id="9045b-288">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="9045b-288">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="9045b-289">A :::no-loc(Create)::: página não tem nenhum estado para inicializar; portanto, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="9045b-289">The :::no-loc(Create)::: page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="9045b-290">Mais adiante no tutorial, você verá o estado de inicialização do método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="9045b-290">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="9045b-291">O `Page` método cria um `PageResult` objeto que renderiza a página *:::no-loc(Create)::: . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9045b-291">The `Page` method creates a `PageResult` object that renders the *:::no-loc(Create):::.cshtml* page.</span></span>

<span data-ttu-id="9045b-292">A `Movie` propriedade usa o atributo [[BindProperty]] <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> para aceitar a associação de [modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="9045b-292">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="9045b-293">Quando o :::no-loc(Create)::: formulário posta os valores de formulário, o tempo de execução de ASP.NET Core associa os valores postados ao `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="9045b-293">When the :::no-loc(Create)::: form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="9045b-294">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="9045b-294">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetPost)]

<span data-ttu-id="9045b-295">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="9045b-295">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="9045b-296">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="9045b-296">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="9045b-297">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="9045b-297">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="9045b-298">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="9045b-298">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="9045b-299">Se não houver nenhum erro de modelo, os dados serão salvos e o navegador será redirecionado para a :::no-loc(Index)::: página.</span><span class="sxs-lookup"><span data-stu-id="9045b-299">If there are no model errors, the data is saved, and the browser is redirected to the :::no-loc(Index)::: page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="9045b-300">A :::no-loc(Create)::: :::no-loc(Razor)::: página</span><span class="sxs-lookup"><span data-stu-id="9045b-300">The :::no-loc(Create)::: :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="9045b-301">Examine o arquivo de página *páginas/filmes/ :::no-loc(Create)::: . cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="9045b-301">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="9045b-302">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9045b-302">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9045b-303">O Visual Studio exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas:</span><span class="sxs-lookup"><span data-stu-id="9045b-303">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Exibição de VS17 da página::: no-Loc (Create):::. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9045b-305">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9045b-305">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9045b-306">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="9045b-306">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9045b-307">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9045b-307">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9045b-308">O Visual Studio para Mac exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas.</span><span class="sxs-lookup"><span data-stu-id="9045b-308">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="9045b-309">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9045b-309">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="9045b-310">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="9045b-310">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="9045b-311">O mecanismo scaffolding cria a :::no-loc(Razor)::: marcação para cada campo no modelo, exceto o ID, semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="9045b-311">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml?range=15-20)]

<span data-ttu-id="9045b-312">Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="9045b-312">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="9045b-313">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="9045b-313">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="9045b-314">A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `[for]` atributo para a `Title` propriedade.</span><span class="sxs-lookup"><span data-stu-id="9045b-314">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="9045b-315">O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="9045b-315">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9045b-316">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9045b-316">Additional resources</span></span>

* [<span data-ttu-id="9045b-317">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="9045b-317">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="9045b-318">[Anterior: adicionar um modelo](xref:tutorials/razor-pages/model) 
>  [Em seguida: trabalhar com um banco de dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="9045b-318">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
