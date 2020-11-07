---
title: Parte 6, adicionar pesquisa
author: rick-anderson
description: Parte 6 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 00c1be2704d92c7d4f868e6eaa346bd8e9901dbf
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360821"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>Parte 6, adicionar pesquisa a ASP.NET Core Razor páginas

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.

Adicione o seguinte realçado usando a instrução e as propriedades a *pages/Movies/ Index . cshtml.cs* :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

No código anterior:

* `SearchString`: Contém o texto que os usuários inserem na caixa de texto de pesquisa. `SearchString` tem o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo. `[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade. `[BindProperty(SupportsGet = true)]` é necessário para a associação em solicitações HTTP GET.
* `Genres`: Contém a lista de gêneros. `Genres` permite que o usuário selecione um gênero na lista. `SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: Contém o gênero específico que o usuário seleciona. Por exemplo, "ocidental".
* `Genres` e `MovieGenre` são abordados mais adiante neste tutorial.

[!INCLUDE[](~/includes/bind-get.md)]

Atualize o Index método da página `OnGetAsync` com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.

Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). As Lambdas são usadas em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operador de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` . Consultas LINQ não são executadas quando são definidas ou quando são modificadas chamando um método, como `Where` , `Contains` ou `OrderBy` . Em vez disso, a execução da consulta é adiada. A avaliação de uma expressão é atrasada até que seu valor percebido seja iterado ou o `ToListAsync` método seja chamado. Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.

> [!NOTE]
> o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#. A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação. No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas. No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.

Navegue até a página filmes e acrescente uma cadeia de caracteres de consulta, como `?searchString=Ghost` à URL. Por exemplo, `https://localhost:5001/Movies?searchString=Ghost`. Os filmes filtrados são exibidos.

![::: no-Loc (índice)::: exibição](search/_static/ghost.png)

Se o modelo de rota a seguir for adicionado à Index página, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL. Por exemplo, `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.  O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.

![::: no-Loc (index)::: View com a palavra fantasma adicionada à URL e uma lista de filmes retornada de dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

O runtime do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`). A associação de modelo é * *_não_* diferencia maiúsculas de minúsculas.

No entanto, não se espera que os usuários modifiquem a URL para pesquisar um filme. Nesta etapa, a interface do usuário é adicionada para filtrar filmes. Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.

Abra o arquivo _Pages/Movies/ Index . cshtml * e adicione a marcação realçada no código a seguir:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):

* [Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper). Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para as *páginas Index /filmes/* página por meio da cadeia de caracteres de consulta.
* [Auxiliar de marcação de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper)

Salve as alterações e teste o filtro.

![::: no-Loc (índice)::: exibição com a palavra fantasma digitada na caixa de texto filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a>Pesquisar por gênero

Atualize o Index método da página `OnGetAsync` com o seguinte código:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Adicionar pesquisa por gênero à Razor página

1. Atualize o *Index . cshtml* [ `<form>` Element] ( https://developer.mozilla.org/docs/Web/HTML/Element/form) conforme realçado na seguinte marcação:

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. Teste o aplicativo pesquisando por gênero, título do filme e por ambos.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: atualizar as páginas](xref:tutorials/razor-pages/da1) 
>  [Em seguida: adicionar um novo campo](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).

Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.

Adicione as seguintes propriedades realçadas a *pages/Movies/ Index . cshtml.cs* :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Contém o texto que os usuários inserem na caixa de texto de pesquisa. `SearchString` tem o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo. `[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade. `[BindProperty(SupportsGet = true)]` é necessário para a associação em solicitações HTTP GET.
* `Genres`: Contém a lista de gêneros. `Genres` permite que o usuário selecione um gênero na lista. `SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: Contém o gênero específico que o usuário seleciona. Por exemplo, "ocidental".
* `Genres` e `MovieGenre` são abordados mais adiante neste tutorial.

[!INCLUDE[](~/includes/bind-get.md)]

Atualize o Index método da página `OnGetAsync` com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.

Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). As Lambdas são usadas em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operador de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` . Consultas LINQ não são executadas quando são definidas ou quando são modificadas chamando um método, como `Where` `Contains`  ou `OrderBy` . Em vez disso, a execução da consulta é adiada. A avaliação de uma expressão é atrasada até que seu valor percebido seja iterado ou o `ToListAsync` método seja chamado. Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.

**Observação:** o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#. A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação. No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas. No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.

Navegue até a página filmes e acrescente uma cadeia de caracteres de consulta, como `?searchString=Ghost` à URL. Por exemplo, `https://localhost:5001/Movies?searchString=Ghost`. Os filmes filtrados são exibidos.

![::: no-Loc (índice)::: exibição](search/_static/ghost.png)

Se o modelo de rota a seguir for adicionado à Index página, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL. Por exemplo, `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.  O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.

![::: no-Loc (index)::: View com a palavra fantasma adicionada à URL e uma lista de filmes retornada de dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

O runtime do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`). A associação de modelo é * *_não_* diferencia maiúsculas de minúsculas.

No entanto, não se espera que os usuários modifiquem a URL para pesquisar um filme. Nesta etapa, a interface do usuário é adicionada para filtrar filmes. Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.

Abra o arquivo _Pages/Movies/ Index . cshtml * e adicione a `<form>` marcação realçada no código a seguir:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):

* [Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper). Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para as *páginas Index /filmes/* página por meio da cadeia de caracteres de consulta.
* [Auxiliar de marcação de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper)

Salve as alterações e teste o filtro.

![::: no-Loc (índice)::: exibição com a palavra fantasma digitada na caixa de texto filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a>Pesquisar por gênero

Atualize o método `OnGetAsync` pelo seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Adicionar pesquisa por gênero à Razor página

Atualize o *Index . cshtml* [ `<form>` Element] ( https://developer.mozilla.org/docs/Web/HTML/Element/form) conforme realçado na seguinte marcação:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Teste o aplicativo pesquisando por gênero, título do filme e por ambos.
O código anterior usa o auxiliar [Select tag](xref:mvc/views/working-with-forms#the-select-tag-helper) e a marca Option Helper.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Anterior: atualizar as páginas](xref:tutorials/razor-pages/da1) 
>  [Em seguida: adicionar um novo campo](xref:tutorials/razor-pages/new-field)

::: moniker-end
