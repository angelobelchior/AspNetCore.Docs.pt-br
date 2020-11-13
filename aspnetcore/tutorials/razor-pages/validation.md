---
title: Parte 8, adicionar validação
author: rick-anderson
description: Parte 8 da série de tutoriais em Razor páginas.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d69ab3452f4f15e916049e5c772a20fe9f9fac65
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570218"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a>Parte 8 da série de tutoriais em Razor páginas.

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesta seção, a lógica de validação é adicionada para o modelo `Movie`. As regras de validação são impostas sempre que um usuário cria ou edita um filme.

## <a name="validation"></a>Validação

Um princípio-chave do desenvolvimento de software é chamado [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (“ **D** on't **R** epeat **Y** ourself”). Razor As páginas incentivam o desenvolvimento onde a funcionalidade é especificada uma vez e é refletida em todo o aplicativo. O DRY pode ajudar a:

* Reduzir a quantidade de código em um aplicativo.
* Fazer com que o código seja menos propenso a erros e mais fácil de ser testado e mantido.

O suporte de validação fornecido por Razor páginas e Entity Framework é um bom exemplo do princípio seco:

* As regras de validação são especificadas declarativamente em um único lugar, na classe de modelo.
* As regras são impostas em todos os lugares no aplicativo.

## <a name="add-validation-rules-to-the-movie-model"></a>Adicionar regras de validação ao modelo de filme

O `DataAnnotations` namespace fornece:

* Um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade.
* Formatação de atributos como `[DataType]` essa ajuda com a formatação e não fornecem nenhuma validação.

Atualize a classe `Movie` para aproveitar os atributos de validação `[Required]`, `[StringLength]`, `[RegularExpression]` e `[Range]` internos.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:

* Os atributos `[Required]` e `[MinimumLength]` indicam que uma propriedade deve ter um valor. Nada impede que um usuário insira espaço em branco para atender a essa validação.
* O atributo `[RegularExpression]` é usado para limitar quais caracteres podem ser inseridos. No código anterior, `Genre` :

  * Deve usar apenas letras.
  * A primeira letra deve ser maiúscula. Espaços em branco, números e caracteres especiais não são permitidos.

* O `RegularExpression` `Rating` :

  * Exige que o primeiro caractere seja uma letra maiúscula.
  * Permite caracteres especiais e números em espaços subsequentes. "PG-13" é válido para uma classificação, mas falha para um `Genre` .

* O atributo `[Range]` restringe um valor a um intervalo especificado.
* O `[StringLength]` atributo pode definir um comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.
* Tipos de valor, como `decimal` ,,, `int` `float` `DateTime` , são inerentemente necessários e não precisam do `[Required]` atributo.

As regras de validação anteriores são usadas para demonstração, elas não são ideais para um sistema de produção. Por exemplo, o anterior impede a inserção de um filme com apenas dois caracteres e não permite caracteres especiais no `Genre` .

Ter regras de validação automaticamente impostas pelo ASP.NET Core ajuda a:

* Ajuda a tornar o aplicativo mais robusto.
* Reduza as chances de salvar dados inválidos no banco de dado.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>IU de erro de validação em Razor páginas

Execute o aplicativo e navegue para Pages/Movies.

Selecione o link **Create novo** . Preencha o formulário com alguns valores inválidos. Quando a validação do lado do cliente do jQuery detecta o erro, ela exibe uma mensagem de erro.

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Observe como o formulário renderizou automaticamente uma mensagem de erro de validação em cada campo que contém um valor inválido. Os erros são impostos no lado do cliente, usando JavaScript e jQuery, e no lado do servidor, quando um usuário tem o JavaScript desabilitado.

Um benefício significativo é que **nenhuma** alteração de código era necessária nas Create páginas ou editar. Depois que as anotações de dados forem aplicadas ao modelo, a interface do usuário de validação foi habilitada. As Razor páginas criadas neste tutorial captam automaticamente as regras de validação, usando atributos de validação nas propriedades da `Movie` classe de modelo. Validação do teste usando a página Editar: a mesma validação é aplicada.

Os dados de formulário não serão postados no servidor enquanto houver erros de validação do lado do cliente. Verifique se os dados de formulário não são postados por uma ou mais das seguintes abordagens:

* Coloque um ponto de interrupção no método `OnPostAsync`. Envie o formulário selecionando **Create** ou **salvando**. O ponto de interrupção nunca é atingido.
* Use a [ferramenta Fiddler](https://www.telerik.com/fiddler).
* Use as ferramentas do desenvolvedor do navegador para monitorar o tráfego de rede.

### <a name="server-side-validation"></a>Validação do servidor

Quando o JavaScript está desabilitado no navegador, o envio do formulário com erros será postado no servidor.

(Opcional) Teste a validação do servidor:

1. Desabilite o JavaScript no navegador. O JavaScript pode ser desabilitado usando as ferramentas de desenvolvedor do navegador. Se o JavaScript não puder ser desabilitado no navegador, tente outro navegador.
1. Defina um ponto de interrupção no `OnPostAsync` método da Create página ou edição.
1. Envie um formulário com dados inválidos.
1. Verifique se o estado do modelo é inválido:

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
Como alternativa, [desabilite a validação do lado do cliente no servidor](xref:mvc/models/validation#disable-client-side-validation).

O código a seguir mostra uma parte da página *Create . cshtml* com Scaffold anteriormente no tutorial. Ele é usado pelas Create páginas e editar para:

* Exibir o formulário inicial.
* Reexiba o formulário no caso de um erro.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) usa os atributos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para a Validação do jQuery no lado do cliente. O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) exibe erros de validação. Consulte [Validação](xref:mvc/models/validation) para obter mais informações.

As Create páginas e editar não têm nenhuma regra de validação. As regras de validação e as cadeias de caracteres de erro são especificadas somente na classe `Movie`. Essas regras de validação são aplicadas automaticamente a Razor páginas que editam o `Movie` modelo.

Quando a lógica de validação precisa ser alterada, ela é feita apenas no modelo. A validação é aplicada consistentemente em todo o aplicativo, a lógica de validação é definida em um único local. A validação em um único lugar ajuda a manter o código limpo e facilita sua manutenção e atualização.

## <a name="use-datatype-attributes"></a>Usar atributos de tipo de dados

Examine a classe `Movie`. O namespace `System.ComponentModel.DataAnnotations` fornece atributos de formatação, além do conjunto interno de atributos de validação. O atributo `[DataType]` é aplicado às propriedades `ReleaseDate` e `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Os `[DataType]` atributos fornecem:

* Dicas para que o mecanismo de exibição formate os dados.
* Fornece atributos como `<a>` para URL e `<a href="mailto:EmailAddress.com">` para email.

Use o atributo `[RegularExpression]` para validar o formato dos dados. O atributo `[DataType]` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados. `[DataType]` atributos não são atributos de validação. No aplicativo de exemplo, apenas a data é exibida, sem a hora.

A `DataType` enumeração fornece muitos tipos de dados, como `Date` ,,, `Time` `PhoneNumber` `Currency` , `EmailAddress` e muito mais. 

Os `[DataType]` atributos:

* Pode permitir que o aplicativo forneça automaticamente recursos específicos de tipo. Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress`.
* Pode fornecer um seletor `DataType.Date` de data em navegadores que dão suporte a HTML5.
* Emitir HTML 5 `data-` , pronunciado "Dash de dados", atributos que o HTML 5 navegadores consomem.
* **Não** forneça nenhuma validação.

`DataType.Date` não especifica o formato da data exibida. Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas `CultureInfo` do servidor.

A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados. Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).

O atributo `[DisplayFormat]` é usado para especificar explicitamente o formato de data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

A `ApplyFormatInEditMode` configuração especifica que a formatação será aplicada quando o valor for exibido para edição. Esse comportamento pode não ser desejado para alguns campos. Por exemplo, em valores de moeda, o símbolo de moeda normalmente não é desejado na interface do usuário de edição.

O atributo `[DisplayFormat]` pode ser usado por si só, mas geralmente é uma boa ideia usar o atributo `[DataType]`. O atributo `[DataType]` transmite a semântica dos dados em vez de como renderizá-los em uma tela. O `[DataType]` atributo fornece os seguintes benefícios que não estão disponíveis com `[DisplayFormat]` :

* O navegador pode habilitar recursos do HTML5, por exemplo, para mostrar um controle de calendário, o símbolo de moeda apropriado da localidade, links de email, etc.
* Por padrão, o navegador renderiza dados usando o formato correto com base em sua localidade.
* O atributo `[DataType]` pode permitir que a estrutura ASP.NET Core escolha o modelo de campo correto para renderizar os dados. O `DisplayFormat` , se usado por si só, usa o modelo de cadeia de caracteres.

**Observação:** a validação do jQuery não funciona com o `[Range]` atributo e `DateTime` . Por exemplo, o seguinte código sempre exibirá um erro de validação do lado do cliente, mesmo quando a data estiver no intervalo especificado:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

É uma prática recomendada evitar a compilação de datas rígidas em modelos, portanto, usando o `[Range]` atributo e `DateTime` não é recomendado. Use a [configuração](xref:fundamentals/configuration/index) para intervalos de datas e outros valores sujeitos a alterações frequentes em vez de especificá-los no código.

O seguinte código mostra como combinar atributos em uma linha:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Introdução ao Razor As páginas e EF Core](xref:data/ef-rp/intro) mostram as operações de EF Core avançadas com Razor páginas.

### <a name="apply-migrations"></a>Aplicar migrações

As anotações de aplicadas à classe alteram o esquema. Por exemplo, as DataAnnotations aplicadas ao campo `Title`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Limitam os caracteres a 60.
* Não permitem um valor `null`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Atualmente a tabela `Movie` tem o seguinte esquema:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

As alterações do esquema anterior não fazem com que o EF lance uma exceção. No entanto, crie uma migração de forma que o esquema seja consistente com o modelo.

 No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.
No PMC, insira os seguintes comandos:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` executa os métodos `Up` da classe `New_DataAnnotations`. Examine o método `Up`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

A tabela `Movie` atualizada tem o seguinte esquema:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

As migrações não são necessárias para o SQLite.

---

### <a name="publish-to-azure"></a>Publicar no Azure

Para obter informações sobre como implantar no Azure, consulte [tutorial: criar um aplicativo ASP.NET Core no Azure com o banco de dados SQL](/azure/app-service/tutorial-dotnetcore-sqldb-app).

Obrigado por concluir esta introdução às Razor páginas. [Introdução ao Razor As páginas e EF Core](xref:data/ef-rp/intro) são um excelente acompanhamento deste tutorial.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [Anterior: adicionar um novo campo](xref:tutorials/razor-pages/new-field)
