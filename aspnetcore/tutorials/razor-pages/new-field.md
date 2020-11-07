---
title: Parte 7, adicionar um novo campo
author: rick-anderson
description: Parte 7 da série de tutoriais em Razor páginas.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2dca5a9552dd2800212f8cd78ace0578b3d38cdb
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360873"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a>Parte 7, adicione um novo campo a uma Razor página no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).

Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:

* Adicionar um novo campo ao modelo.
* Migrar a nova alteração de esquema de campo para o banco de dados.

Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:

* Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.
* Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.

A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Adicionando uma propriedade de classificação ao modelo de filme

1. Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Crie o aplicativo.

1. Edite *pages/Movies/ Index . cshtml* e adicione um `Rating` campo:

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Atualize as seguintes páginas:
   1. Adicione o `Rating` campo às Delete páginas e detalhes.
   1. Update [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) com um `Rating` campo.
   1. Adicione o campo `Rating` à página Editar.

O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo. Executar o aplicativo sem uma atualização para o banco de dados gera um `SqlException` :

`SqlException: Invalid column name 'Rating'.`

A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados. Não há nenhuma `Rating` coluna na tabela de banco de dados.

Existem algumas abordagens para resolver o erro:

1. Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo. Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos. A desvantagem é que você perde os dados existentes no banco de dados. Não use essa abordagem em um banco de dados de produção. O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.

2. Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo. A vantagem dessa abordagem é manter os dados. Faça essa alteração manualmente ou criando um script de alteração de banco de dados.

3. Use as Migrações do Code First para atualizar o esquema de banco de dados.

Para este tutorial, use as Migrações do Code First.

Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna. Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).

Compile a solução.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Adicionar uma migração para o campo de classificação

1.  No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.
2. No PMC, insira os seguintes comandos:

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

O comando `Add-Migration` informa à estrutura:

* Compare o `Movie` modelo com o `Movie` esquema de banco de dados.
* Create código para migrar o esquema de banco de dados para o novo modelo.

O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração. É útil usar um nome significativo para o arquivo de migração.

O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.

<a name="ssox"></a>

Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo. Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).

Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados. Para excluir o banco de dados no SSOX:

1. Selecione o banco de dados no SSOX.
1. Clique com o botão direito do mouse no banco de dados e selecione **Delete** .
1. Marque **fechar conexões existentes**.
1. Selecione **OK**.
1. No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Remover e recriar o banco de dados

> [!NOTE]
> Para este tutorial, você usa o recurso de *migrações* Entity Framework Core sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna. Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha. Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite. Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.
>
>A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. Uma recompilação de tabela envolve:
>
>* Criar uma nova tabela.
>* Copiar dados da tabela antiga para a nova tabela.
>* Remover a tabela antiga.
>* Renomear a nova tabela.
>
>Para obter mais informações, consulte os seguintes recursos:
>
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
> * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

1. Delete a pasta de migração.  

1. Use os comandos a seguir para recriar o banco de dados.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`. Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).

Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:

* Adicionar um novo campo ao modelo.
* Migrar a nova alteração de esquema de campo para o banco de dados.

Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:

* Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.
* Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.

A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Adicionando uma propriedade de classificação ao modelo de filme

1. Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Crie o aplicativo.

1. Edite *pages/Movies/ Index . cshtml* e adicione um `Rating` campo:

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Atualize as seguintes páginas:
   1. Adicione o `Rating` campo às Delete páginas e detalhes.
   1. Update [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) com um `Rating` campo.
   1. Adicione o campo `Rating` à página Editar.

O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo. Executar o aplicativo sem uma atualização para o banco de dados gera um `SqlException` :

`SqlException: Invalid column name 'Rating'.`

A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados. Não há nenhuma `Rating` coluna na tabela de banco de dados.

Existem algumas abordagens para resolver o erro:

1. Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo. Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos. A desvantagem é que você perde os dados existentes no banco de dados. Não use essa abordagem em um banco de dados de produção. O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.

2. Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo. A vantagem dessa abordagem é manter os dados. Faça essa alteração manualmente ou criando um script de alteração de banco de dados.

3. Use as Migrações do Code First para atualizar o esquema de banco de dados.

Para este tutorial, use as Migrações do Code First.

Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna. Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).

Compile a solução.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Adicionar uma migração para o campo de classificação

1.  No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.
2. No PMC, insira os seguintes comandos:

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

O comando `Add-Migration` informa à estrutura:

* Compare o `Movie` modelo com o `Movie` esquema de banco de dados.
* Create código para migrar o esquema de banco de dados para o novo modelo.

O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração. É útil usar um nome significativo para o arquivo de migração.

O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.

<a name="ssox"></a>

Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo. Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).

Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados. Para excluir o banco de dados no SSOX:

* Selecione o banco de dados no SSOX.
* Clique com o botão direito do mouse no banco de dados e selecione **Delete** .
* Marque **fechar conexões existentes**.
* Selecione **OK**.
* No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Remover e recriar o banco de dados

> [!NOTE]
> Para este tutorial, use o recurso de *migrações* Entity Framework Core sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna. Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha. Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite. Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.
>
>A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. Uma recompilação de tabela envolve:
>
>* Criar uma nova tabela.
>* Copiar dados da tabela antiga para a nova tabela.
>* Remover a tabela antiga.
>* Renomear a nova tabela.
>
>Para obter mais informações, consulte os seguintes recursos:
>
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
> * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

1. Delete a pasta de migração.  

1. Use os comandos a seguir para recriar o banco de dados.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`. Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).

Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:

* Adicionar um novo campo ao modelo.
* Migrar a nova alteração de esquema de campo para o banco de dados.

Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:

* Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.
* Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.

A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Adicionando uma propriedade de classificação ao modelo de filme

Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Crie o aplicativo.

Edite *pages/Movies/ Index . cshtml* e adicione um `Rating` campo:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Atualize as seguintes páginas:

* Adicione o `Rating` campo às Delete páginas e detalhes.
* Update [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) com um `Rating` campo.
* Adicione o campo `Rating` à página Editar.

O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo. Se o aplicativo for executado agora, o aplicativo lançará `SqlException` :

`SqlException: Invalid column name 'Rating'.`

Esse erro é causado devido à classe de modelo Movie atualizada ser diferente do esquema da tabela Movie do banco de dados. Não há nenhuma `Rating` coluna na tabela de banco de dados.

Existem algumas abordagens para resolver o erro:

1. Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo. Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos. A desvantagem é que você perde os dados existentes no banco de dados. Não use essa abordagem em um banco de dados de produção. O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.

2. Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo. A vantagem dessa abordagem é manter os dados. Faça essa alteração manualmente ou criando um script de alteração de banco de dados.

3. Use as Migrações do Code First para atualizar o esquema de banco de dados.

Para este tutorial, use as Migrações do Code First.

Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna. Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Compile a solução.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Adicionar uma migração para o campo de classificação

 No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.
No PMC, insira os seguintes comandos:

```powershell
Add-Migration Rating
Update-Database
```

O comando `Add-Migration` informa à estrutura:

* Compare o `Movie` modelo com o `Movie` esquema de banco de dados.
* Create código para migrar o esquema de banco de dados para o novo modelo.

O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração. É útil usar um nome significativo para o arquivo de migração.

O comando `Update-Database` informa à estrutura para aplicar as alterações de esquema no banco de dados.

<a name="ssox"></a>

Se você excluir todos os registros no DdatabaseB, o inicializador propagará o DdatabaseB e incluirá o `Rating` campo. Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).

Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados. Para excluir o banco de dados no SSOX:

* Selecione o banco de dados no SSOX.
* Clique com o botão direito do mouse no banco de dados e selecione **Delete** .
* Marque **fechar conexões existentes**.
* Selecione **OK**.
* No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Remover e recriar o banco de dados

> [!NOTE]
> Para este tutorial, use o recurso de *migrações* Entity Framework Core sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna. Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha. Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite. Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.
>
>A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. Uma recompilação de tabela envolve:
>
>* Criar uma nova tabela.
>* Copiar dados da tabela antiga para a nova tabela.
>* Remover a tabela antiga.
>* Renomear a nova tabela.
>
>Para obter mais informações, consulte os seguintes recursos:
>
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
> * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

Delete o banco de dados e usa migrações para recriar o banco de dados. Para excluir o banco de dados, exclua o arquivo de banco de dados ( *MvcMovie.db* ). Em seguida, execute o comando `ef database update`:

```dotnetcli
dotnet ef database update
```

---

Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`. Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)

::: moniker-end
