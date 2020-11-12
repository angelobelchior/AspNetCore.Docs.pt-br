---
title: Parte 2, adicionar um modelo
author: rick-anderson
description: Parte 2 da série de tutoriais em Razor páginas. Nesta seção, as classes de modelo são adicionadas.
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550656"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Parte 2, adicionar um modelo a um Razor aplicativo de páginas no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados. As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados. EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados. Primeiro, você escreve as classes de modelo e EF Core cria o banco de dados.

As classes de modelo são conhecidas como classes POCO (de " **P** Lain- **o** LD **C** LR **o** bjects") porque não têm uma dependência em EF Core. Elas definem as propriedades dos dados que são armazenados no banco de dados.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Em **Gerenciador de soluções** , clique com o botão direito do mouse no projeto *Razor PagesMovie* > **Adicionar**  >  **nova pasta**. Nomeie a pasta como *Modelos*.
1. Clique com o botão direito do mouse na pasta *modelos* . Selecione **Adicionar**  >  **classe**. Dê à classe o nome *Movie*.
1. Adicione as seguintes propriedades à classe `Movie`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não é solicitado a inserir informações de hora no campo de data.
  * Somente a data é exibida, não as informações de tempo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Adicione uma pasta denominada *Modelos*.
1. Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Adicionar pacotes NuGet e ferramentas de EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Adicionar uma classe de contexto de banco de dados

1. No projeto *Razor PagesMovie* , crie uma pasta chamada *Data*.
1. Na pasta *dados* , adicione um arquivo chamado *Razor PagesMovieContext.cs* com o seguinte código:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   O código anterior cria uma propriedade `DbSet` para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela. O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

1. Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na **janela ferramenta de solução** , clique com o botão de controle no projeto *Razor PagesMovie* e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.
1. Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo...**.
1. Na caixa de diálogo **Novo Arquivo** :
   1. Selecione **Geral** no painel esquerdo.
   1. Selecione **Classe Vazia** no painel central.
   1. Nomeie a classe **Movie** e selecione **Novo**.

1. Adicione as seguintes propriedades à classe `Movie`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

Crie o projeto para verificar se não há erros de compilação.

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta scaffolding produz páginas para Create operações de, ler, atualizar e Delete (CRUD) para o modelo de filme.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Create uma pasta de *páginas/filmes* :
   1. Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.
   1. Nomeie a pasta *filmes*.

1. Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.

   ![Imagem das instruções anteriores.](model/_static/5/sca.png)

1. Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.

   ![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

1. Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :
   1. Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.
   1. Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).
      1. Na caixa de diálogo **adicionar contexto de dados** , o nome da classe *Razor PagesMovie. Data. Razor PagesMovieContext* é gerado.
   1. Selecione **Adicionar**.

   ![Imagem das instruções anteriores.](model/_static/3/arp.png)

O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra um shell de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .

* **Para Windows** : execute o seguinte comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **No macOS e Linux** , execute o comando a seguir:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> A tabela a seguir detalha as opções do gerador de código ASP.NET Core.

| Opção               | Descrição|
| ----------------- | ------------ |
| `-m`  | O nome do modelo. |
| `-dc`  | A classe `DbContext` a ser usada. |
| `-udl` | Use o layout padrão. |
| `-outDir` | O caminho da pasta de saída relativa para criar as exibições. |
| `--referenceScriptLibraries` | Adiciona `_ValidationScriptsPartial` a edição e Create páginas |

Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usar o SQLite para desenvolvimento, SQL Server para produção

Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento. O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` . `IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Create uma pasta de *páginas/filmes* :
   1. Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.
   1. Nomeie a pasta *filmes*.

1. Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.

   ![Imagem das instruções anteriores.](model/_static/scaMac.png)

1. Na caixa de diálogo **novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Avançar**.

   ![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

1. Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :
   1. Na **classe DbContext a ser usada:** Row, nomeie a classe *Razor PagesMovie. Data. Razor PagesMovieContext*.
   1. Selecione **Concluir**.

   ![Imagem das instruções anteriores.](model/_static/5/arpMac.png)

O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usar o SQLite para desenvolvimento, SQL Server para produção

Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento. O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` . `IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Arquivos criados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O processo de scaffold cria e atualiza os arquivos a seguir:

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .
* *Data/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O processo de scaffold cria os arquivos a seguir:

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .

Os arquivos criados são explicados na próxima seção.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

O processo de scaffold cria e atualiza os arquivos a seguir:

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .
* *Data/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a>Create o esquema de banco de dados inicial usando o recurso de migração do EF

O recurso de migrações no Entity Framework Core fornece uma maneira de:

* Create o esquema de banco de dados inicial.
* Atualize incrementalmente o esquema de banco de dados para mantê-lo em sincronia com o modelo de dado do aplicativo.  O banco de dados existente é preservado.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nesta seção, a janela do **Package Manager Console** (PMC) é usada para:

* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

1. No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

   ![Menu do PMC](model/_static/5/pmc.png)

1. No PMC, insira os seguintes comandos:

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Execute os seguintes comandos da CLI do .NET:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”

Ignore o aviso, pois ele será abordado em uma etapa posterior.

O comando `migrations` gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado em `DbContext` . O argumento `InitialCreate` é usado para nomear as migrações. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.

O `update` comando executa o `Up` método em migrações que não foram aplicadas. Nesse caso, `update` o executa o `Up` método no arquivo *migrations/ \<time-stamp> _Initial Create . cs* , que cria o banco de dados.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços, como o contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.

A ferramenta scaffolding criou automaticamente um contexto de banco de dados e o registrou com o contêiner de injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como Create ler, atualizar e Delete , para o `Movie` modelo. O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). O contexto de dados especifica quais entidades são incluídas no modelo de dados.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma [propriedade \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Examine o método `Up`.

---

<a name="test"></a>

## <a name="test-the-app"></a>Testar o aplicativo

1. Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

   Se você receber o seguinte erro:

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   Você perdeu a [etapa de migrações](#pmc).

1. Teste o **Create** link.

   ![::: no-Loc (criar)::: página](model/_static/conan5.png)

   > [!NOTE]
   > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

1. Teste a **edição** , os **detalhes** e os **Delete** links.

O tutorial a seguir explica os arquivos criados por scaffolding.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Nesta seção, as classes são adicionadas para o gerenciamento de filmes. As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados. EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.

As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core. Elas definem as propriedades dos dados que são armazenados no banco de dados.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse no projeto **Razor PagesMovie** > **Adicionar**  >  **nova pasta**. Nomeie a pasta como *Modelos*.

Clique com o botão direito do mouse na pasta *modelos* . Selecione **Adicionar**  >  **classe**. Dê à classe o nome **Movie**.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta denominada *Modelos*.
* Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Adicionar pacotes NuGet e ferramentas de EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Adicionar uma classe de contexto de banco de dados

* No projeto *Razor PagesMovie* , crie uma nova pasta denominada *dados*.
* Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados* :

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade `DbSet` para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela. O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Na **janela ferramenta de solução** , clique com o botão de controle no projeto **Razor PagesMovie** e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.
* Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo...**.
* Na caixa de diálogo **Novo Arquivo** :

  * Selecione **Geral** no painel esquerdo.
  * Selecione **Classe Vazia** no painel central.
  * Nomeie a classe **Movie** e selecione **Novo**.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

Crie o projeto para verificar se não há erros de compilação.

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta scaffolding produz páginas para Create operações de, ler, atualizar e Delete (CRUD) para o modelo de filme.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Create uma pasta de *páginas/filmes* :

* Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.
* Nomeie a pasta *filmes*.

Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.

![Imagem das instruções anteriores.](model/_static/sca.png)

Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :

* Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.
* Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e altere o nome gerado de Razor PagesMovie. **Modelos**. Razor PagesMovieContext Razor PagesMovie. **Dados**. Razor PagesMovieContext. [Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória. Ela cria a classe de contexto do banco de dados com o namespace correto.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/3/arp.png)

O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .

* **Para Windows** : execute o seguinte comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **No macOS e Linux** , execute o comando a seguir:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> A tabela a seguir detalha as opções do gerador de código ASP.NET Core:

| Opção               | Descrição|
| ----------------- | ------------ |
| `-m`  | O nome do modelo. |
| `-dc`  | A classe `DbContext` a ser usada. |
| `-udl` | Use o layout padrão. |
| `-outDir` | O caminho da pasta de saída relativa para criar as exibições. |
| `--referenceScriptLibraries` | Adiciona `_ValidationScriptsPartial` a edição e Create páginas |

Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usar o SQLite para desenvolvimento, SQL Server para produção

Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento. O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização. `IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Create uma pasta de *páginas/filmes* :

* Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.
* Nomeie a pasta *filmes*.

Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.

![Imagem das instruções anteriores.](model/_static/scaMac.png)

Na caixa de diálogo **novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Avançar**.

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :

* Na lista suspensa **classe de modelo** , selecione ou digite **filme ( Razor PagesMovie. Models)**.
* Na linha **classe de contexto de dados** , digite o nome da nova classe, Razor PagesMovie. **Dados**. Razor PagesMovieContext. [Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória. Ela cria a classe de contexto do banco de dados com o namespace correto.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arpMac.png)

O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

### <a name="add-ef-tools"></a>Adicionar ferramentas do EF

Execute o seguinte comando de CLI do .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef
```

O comando anterior adiciona as ferramentas de Entity Framework Core para o CLI do .NET Core. Para obter mais informações, consulte [referência de ferramentas de Entity Framework Core-CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usar o SQLite para desenvolvimento, SQL Server para produção

Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento. O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização. `IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Arquivos criados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O processo de scaffold cria e atualiza os arquivos a seguir:

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .
* *Data/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

O processo de scaffold cria e atualiza os arquivos a seguir:

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .
* *Data/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O processo de scaffold cria os arquivos a seguir:

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .

Os arquivos criados são explicados na próxima seção.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migração inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:

* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Execute os seguintes comandos da CLI do .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”

Ignore o aviso, pois ele será abordado em uma etapa posterior.

O comando migrações gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado em `DbContext` . O argumento `InitialCreate` é usado para nomear as migrações. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.

O `update` comando executa o `Up` método em migrações que não foram aplicadas. Nesse caso, `update` o executa o `Up` método no arquivo  *migrations/ \<time-stamp> _Initial Create . cs* , que cria o banco de dados.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços, como o contexto de contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços, como Razor páginas, são fornecidos a esses serviços por meio de parâmetros do construtor. O código do construtor que obtém uma instância de contexto de contexto de banco de dados é mostrado posteriormente no tutorial.

A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como Create ler, atualizar e Delete , para o `Movie` modelo. O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). O contexto de dados especifica quais entidades são incluídas no modelo de dados.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Examine o método `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

Se você obtiver o erro:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

* Teste o **Create** link.

  ![::: no-Loc (criar)::: página](model/_static/conan5.png)

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Teste a **edição** , os **detalhes** e os **Delete** links.

O tutorial a seguir explica os arquivos criados por scaffolding.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada. Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite. As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados. O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.

As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core. Elas definem as propriedades dos dados que são armazenados no banco de dados.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse no projeto **Razor PagesMovie** > **Adicionar**  >  **nova pasta**. Nomeie a pasta como *Modelos*.

Clique com o botão direito do mouse na pasta *modelos* . Selecione **Adicionar**  >  **classe**. Dê à classe o nome **Movie**.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta denominada *Modelos*.
* Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Adicionar pacotes NuGet e ferramentas de EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Adicionar uma classe de contexto de banco de dados

No Razor projeto PagesMovie, crie uma nova pasta denominada *dados*. 
Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade `DbSet` para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela. O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Adicionar os pacotes NuGet necessários

Execute o seguinte comando CLI do .NET Core para adicionar SQLite e CodeGeneration. Design ao projeto:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Compile o projeto como uma verificação de erros.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Na **janela da ferramenta de solução** , clique com o botão de controle no projeto *Razor PagesMovie* e selecione **Adicionar**  >  **nova pasta**. Nomeie a pasta como *Modelos*.
* Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo**.
* Na caixa de diálogo **Novo Arquivo** :

  * Selecione **Geral** no painel esquerdo.
  * Selecione **Classe Vazia** no painel central.
  * Nomeie a classe **Movie** e selecione **Novo**.

Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.

---

Crie o projeto para verificar se não há erros de compilação.

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta scaffolding produz páginas para Create operações de, ler, atualizar e Delete (CRUD) para o modelo de filme.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Create uma pasta de *páginas/filmes* :

* Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.
* Nomeie a pasta *filmes*.

Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.

![Imagem das instruções anteriores.](model/_static/sca.png)

Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.
* Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e aceite o nome gerado **Razor PagesMovie. Models. Razor PagesMovieContext**.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arp.png)

O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .

* **Para Windows** : execute o seguinte comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **No macOS e Linux** , execute o comando a seguir:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> A tabela a seguir detalha as opções do gerador de código ASP.NET Core:

| Opção               | Descrição|
| ----------------- | ------------ |
| `-m`  | O nome do modelo. |
| `-dc`  | A classe `DbContext` a ser usada. |
| `-udl` | Use o layout padrão. |
| `-outDir` | O caminho da pasta de saída relativa para criar as exibições. |
| `--referenceScriptLibraries` | Adiciona `_ValidationScriptsPartial` a edição e Create páginas |

Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Create uma pasta de *páginas/filmes* :

* Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.
* Nomeie a pasta *filmes*.

Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.

![Imagem das instruções anteriores.](model/_static/scaMac.png)

Na caixa de diálogo **Adicionar novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :

* Na lista suspensa **classe de modelo** , selecione ou digite **filme**.
* Na linha **classe de contexto de dados** , digite Select **Razor PagesMovieContext** isso criará uma nova classe de contexto de banco de dado com o namespace correto. Nesse caso, será **Razor PagesMovie. Models. Razor PagesMovieContext**.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arpMac.png)

O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

---

O processo de scaffold cria e atualiza os arquivos a seguir:

### <a name="files-created"></a>Arquivos criados

* *Páginas/filmes* : Create , Delete , detalhes, editar e Index .
* *Data/ Razor PagesMovieContext.cs*

### <a name="file-updated"></a>Arquivo atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migração inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:

* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```powershell
Add-Migration Initial
Update-Database
```

O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado no `DbContext` , no arquivo *Razor PagesMovieContext.cs* . O `InitialCreate` argumento é usado para nomear a migração. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado. Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.

O `Update-Database` comando executa o `Up` método no arquivo *migrations/ \<time-stamp> _Initial Create . cs* . O método `Up` cria o banco de dados.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Execute os seguintes comandos da CLI do .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”

Ignore o aviso, pois ele será abordado em uma etapa posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de contexto de banco de dados EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor. O código do construtor que obtém uma instância de contexto contextB de contexto do banco de dados é mostrado posteriormente no tutorial.

A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como Create ler, atualizar e Delete , para o `Movie` modelo. O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). O contexto de dados especifica quais entidades são incluídas no modelo de dados.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Examine o método `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

Se você obtiver o erro:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

* Teste o **Create** link.

  ![::: no-Loc (criar)::: página](model/_static/conan.png)

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Teste a **edição** , os **detalhes** e os **Delete** links.

O tutorial a seguir explica os arquivos criados por scaffolding.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do

::: moniker-end
