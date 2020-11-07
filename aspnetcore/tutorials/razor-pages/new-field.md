---
title: Parte 7, adicionar um novo campo
author: rick-anderson
description: 'Parte 7 da série de tutoriais em :::no-loc(Razor)::: páginas.'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2dca5a9552dd2800212f8cd78ace0578b3d38cdb
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360873"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="89b31-103">Parte 7, adicione um novo campo a uma :::no-loc(Razor)::: página no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89b31-103">Part 7, add a new field to a :::no-loc(Razor)::: Page in ASP.NET Core</span></span>

<span data-ttu-id="89b31-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="89b31-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="89b31-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="89b31-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="89b31-106">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="89b31-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="89b31-107">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-107">Add a new field to the model.</span></span>
* <span data-ttu-id="89b31-108">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="89b31-109">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="89b31-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="89b31-110">Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="89b31-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="89b31-111">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="89b31-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="89b31-112">A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="89b31-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="89b31-113">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="89b31-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="89b31-114">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="89b31-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="89b31-115">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89b31-115">Build the app.</span></span>

1. <span data-ttu-id="89b31-116">Edite *pages/Movies/ :::no-loc(Index)::: . cshtml* e adicione um `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="89b31-116">Edit *Pages/Movies/:::no-loc(Index):::.cshtml* , and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/SnapShots/:::no-loc(Index):::Rating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="89b31-117">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="89b31-117">Update the following pages:</span></span>
   1. <span data-ttu-id="89b31-118">Adicione o `Rating` campo às :::no-loc(Delete)::: páginas e detalhes.</span><span class="sxs-lookup"><span data-stu-id="89b31-118">Add the `Rating` field to the :::no-loc(Delete)::: and Details pages.</span></span>
   1. <span data-ttu-id="89b31-119">Update [ :::no-loc(Create)::: . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Pages/Movies/:::no-loc(Create):::.cshtml) com um `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-119">Update [:::no-loc(Create):::.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Pages/Movies/:::no-loc(Create):::.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="89b31-120">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="89b31-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="89b31-121">O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="89b31-122">Executar o aplicativo sem uma atualização para o banco de dados gera um `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="89b31-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="89b31-123">A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="89b31-124">Não há nenhuma `Rating` coluna na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="89b31-125">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="89b31-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="89b31-126">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="89b31-127">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="89b31-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="89b31-128">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="89b31-129">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="89b31-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="89b31-130">O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89b31-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="89b31-131">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="89b31-132">A vantagem dessa abordagem é manter os dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="89b31-133">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="89b31-134">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="89b31-135">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="89b31-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="89b31-136">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="89b31-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="89b31-137">Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.</span><span class="sxs-lookup"><span data-stu-id="89b31-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="89b31-138">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="89b31-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="89b31-139">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="89b31-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89b31-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89b31-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="89b31-141">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="89b31-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="89b31-142"> No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="89b31-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="89b31-143">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="89b31-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="89b31-144">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="89b31-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="89b31-145">Compare o `Movie` modelo com o `Movie` esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="89b31-146">:::no-loc(Create)::: código para migrar o esquema de banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-146">:::no-loc(Create)::: code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="89b31-147">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="89b31-148">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="89b31-149">O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.</span><span class="sxs-lookup"><span data-stu-id="89b31-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="89b31-150">Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="89b31-151">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="89b31-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="89b31-152">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="89b31-153">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="89b31-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="89b31-154">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="89b31-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="89b31-155">Clique com o botão direito do mouse no banco de dados e selecione **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="89b31-155">Right-click on the database, and select **:::no-loc(Delete):::**.</span></span>
1. <span data-ttu-id="89b31-156">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="89b31-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="89b31-157">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="89b31-157">Select **OK**.</span></span>
1. <span data-ttu-id="89b31-158">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="89b31-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89b31-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="89b31-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="89b31-160">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="89b31-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="89b31-161">Para este tutorial, você usa o recurso de *migrações* Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="89b31-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="89b31-162">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="89b31-163">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="89b31-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="89b31-164">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="89b31-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="89b31-165">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="89b31-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="89b31-166">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="89b31-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="89b31-167">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="89b31-168">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="89b31-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="89b31-169">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="89b31-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="89b31-170">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-170">Creating a new table.</span></span>
>* <span data-ttu-id="89b31-171">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="89b31-172">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="89b31-172">Dropping the old table.</span></span>
>* <span data-ttu-id="89b31-173">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-173">Renaming the new table.</span></span>
>
><span data-ttu-id="89b31-174">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="89b31-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="89b31-175">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="89b31-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="89b31-176">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="89b31-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="89b31-177">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="89b31-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="89b31-178">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="89b31-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="89b31-179">:::no-loc(Delete)::: a pasta de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-179">:::no-loc(Delete)::: the migration folder.</span></span>  

1. <span data-ttu-id="89b31-180">Use os comandos a seguir para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add Initial:::no-loc(Create):::
   dotnet ef database update
   ```

---

<span data-ttu-id="89b31-181">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="89b31-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="89b31-182">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="89b31-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89b31-183">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="89b31-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="89b31-184">[Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="89b31-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="89b31-185">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="89b31-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="89b31-186">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="89b31-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="89b31-187">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-187">Add a new field to the model.</span></span>
* <span data-ttu-id="89b31-188">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="89b31-189">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="89b31-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="89b31-190">Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="89b31-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="89b31-191">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="89b31-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="89b31-192">A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="89b31-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="89b31-193">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="89b31-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="89b31-194">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="89b31-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="89b31-195">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89b31-195">Build the app.</span></span>

1. <span data-ttu-id="89b31-196">Edite *pages/Movies/ :::no-loc(Index)::: . cshtml* e adicione um `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="89b31-196">Edit *Pages/Movies/:::no-loc(Index):::.cshtml* , and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/:::no-loc(Index):::Rating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="89b31-197">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="89b31-197">Update the following pages:</span></span>
   1. <span data-ttu-id="89b31-198">Adicione o `Rating` campo às :::no-loc(Delete)::: páginas e detalhes.</span><span class="sxs-lookup"><span data-stu-id="89b31-198">Add the `Rating` field to the :::no-loc(Delete)::: and Details pages.</span></span>
   1. <span data-ttu-id="89b31-199">Update [ :::no-loc(Create)::: . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml) com um `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-199">Update [:::no-loc(Create):::.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="89b31-200">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="89b31-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="89b31-201">O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="89b31-202">Executar o aplicativo sem uma atualização para o banco de dados gera um `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="89b31-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="89b31-203">A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="89b31-204">Não há nenhuma `Rating` coluna na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="89b31-205">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="89b31-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="89b31-206">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="89b31-207">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="89b31-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="89b31-208">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="89b31-209">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="89b31-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="89b31-210">O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89b31-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="89b31-211">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="89b31-212">A vantagem dessa abordagem é manter os dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="89b31-213">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="89b31-214">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="89b31-215">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="89b31-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="89b31-216">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="89b31-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="89b31-217">Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.</span><span class="sxs-lookup"><span data-stu-id="89b31-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="89b31-218">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="89b31-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="89b31-219">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="89b31-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89b31-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89b31-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="89b31-221">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="89b31-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="89b31-222"> No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="89b31-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="89b31-223">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="89b31-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="89b31-224">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="89b31-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="89b31-225">Compare o `Movie` modelo com o `Movie` esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="89b31-226">:::no-loc(Create)::: código para migrar o esquema de banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-226">:::no-loc(Create)::: code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="89b31-227">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="89b31-228">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="89b31-229">O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.</span><span class="sxs-lookup"><span data-stu-id="89b31-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="89b31-230">Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="89b31-231">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="89b31-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="89b31-232">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="89b31-233">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="89b31-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="89b31-234">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="89b31-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="89b31-235">Clique com o botão direito do mouse no banco de dados e selecione **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="89b31-235">Right-click on the database, and select **:::no-loc(Delete):::**.</span></span>
* <span data-ttu-id="89b31-236">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="89b31-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="89b31-237">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="89b31-237">Select **OK**.</span></span>
* <span data-ttu-id="89b31-238">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="89b31-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89b31-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="89b31-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="89b31-240">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="89b31-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="89b31-241">Para este tutorial, use o recurso de *migrações* Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="89b31-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="89b31-242">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="89b31-243">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="89b31-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="89b31-244">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="89b31-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="89b31-245">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="89b31-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="89b31-246">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="89b31-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="89b31-247">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="89b31-248">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="89b31-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="89b31-249">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="89b31-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="89b31-250">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-250">Creating a new table.</span></span>
>* <span data-ttu-id="89b31-251">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="89b31-252">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="89b31-252">Dropping the old table.</span></span>
>* <span data-ttu-id="89b31-253">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-253">Renaming the new table.</span></span>
>
><span data-ttu-id="89b31-254">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="89b31-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="89b31-255">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="89b31-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="89b31-256">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="89b31-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="89b31-257">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="89b31-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="89b31-258">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="89b31-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="89b31-259">:::no-loc(Delete)::: a pasta de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-259">:::no-loc(Delete)::: the migration folder.</span></span>  

1. <span data-ttu-id="89b31-260">Use os comandos a seguir para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add Initial:::no-loc(Create):::
   dotnet ef database update
   ```

---

<span data-ttu-id="89b31-261">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="89b31-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="89b31-262">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="89b31-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89b31-263">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="89b31-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="89b31-264">[Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="89b31-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89b31-265">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="89b31-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="89b31-266">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="89b31-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="89b31-267">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-267">Add a new field to the model.</span></span>
* <span data-ttu-id="89b31-268">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="89b31-269">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="89b31-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="89b31-270">Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="89b31-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="89b31-271">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="89b31-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="89b31-272">A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="89b31-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="89b31-273">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="89b31-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="89b31-274">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="89b31-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="89b31-275">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89b31-275">Build the app.</span></span>

<span data-ttu-id="89b31-276">Edite *pages/Movies/ :::no-loc(Index)::: . cshtml* e adicione um `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="89b31-276">Edit *Pages/Movies/:::no-loc(Index):::.cshtml* , and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Index):::Rating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="89b31-277">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="89b31-277">Update the following pages:</span></span>

* <span data-ttu-id="89b31-278">Adicione o `Rating` campo às :::no-loc(Delete)::: páginas e detalhes.</span><span class="sxs-lookup"><span data-stu-id="89b31-278">Add the `Rating` field to the :::no-loc(Delete)::: and Details pages.</span></span>
* <span data-ttu-id="89b31-279">Update [ :::no-loc(Create)::: . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Create):::.cshtml) com um `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-279">Update [:::no-loc(Create):::.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Create):::.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="89b31-280">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="89b31-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="89b31-281">O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="89b31-282">Se o aplicativo for executado agora, o aplicativo lançará `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="89b31-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="89b31-283">Esse erro é causado devido à classe de modelo Movie atualizada ser diferente do esquema da tabela Movie do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="89b31-284">Não há nenhuma `Rating` coluna na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="89b31-285">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="89b31-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="89b31-286">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="89b31-287">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="89b31-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="89b31-288">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="89b31-289">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="89b31-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="89b31-290">O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89b31-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="89b31-291">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="89b31-292">A vantagem dessa abordagem é manter os dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="89b31-293">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="89b31-294">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="89b31-295">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="89b31-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="89b31-296">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="89b31-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="89b31-297">Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.</span><span class="sxs-lookup"><span data-stu-id="89b31-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="89b31-298">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="89b31-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="89b31-299">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="89b31-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89b31-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89b31-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="89b31-301">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="89b31-301">Add a migration for the rating field</span></span>

<span data-ttu-id="89b31-302"> No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="89b31-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="89b31-303">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="89b31-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="89b31-304">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="89b31-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="89b31-305">Compare o `Movie` modelo com o `Movie` esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="89b31-306">:::no-loc(Create)::: código para migrar o esquema de banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="89b31-306">:::no-loc(Create)::: code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="89b31-307">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="89b31-308">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="89b31-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="89b31-309">O comando `Update-Database` informa à estrutura para aplicar as alterações de esquema no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="89b31-310">Se você excluir todos os registros no DdatabaseB, o inicializador propagará o DdatabaseB e incluirá o `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="89b31-310">If you delete all the records in the DdatabaseB, the initializer will seed the DdatabaseB and include the `Rating` field.</span></span> <span data-ttu-id="89b31-311">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="89b31-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="89b31-312">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="89b31-313">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="89b31-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="89b31-314">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="89b31-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="89b31-315">Clique com o botão direito do mouse no banco de dados e selecione **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="89b31-315">Right-click on the database, and select **:::no-loc(Delete):::**.</span></span>
* <span data-ttu-id="89b31-316">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="89b31-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="89b31-317">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="89b31-317">Select **OK**.</span></span>
* <span data-ttu-id="89b31-318">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="89b31-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89b31-319">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="89b31-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="89b31-320">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="89b31-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="89b31-321">Para este tutorial, use o recurso de *migrações* Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="89b31-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="89b31-322">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="89b31-323">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="89b31-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="89b31-324">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="89b31-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="89b31-325">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="89b31-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="89b31-326">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="89b31-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="89b31-327">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="89b31-328">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="89b31-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="89b31-329">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="89b31-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="89b31-330">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-330">Creating a new table.</span></span>
>* <span data-ttu-id="89b31-331">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="89b31-332">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="89b31-332">Dropping the old table.</span></span>
>* <span data-ttu-id="89b31-333">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="89b31-333">Renaming the new table.</span></span>
>
><span data-ttu-id="89b31-334">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="89b31-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="89b31-335">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="89b31-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="89b31-336">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="89b31-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="89b31-337">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="89b31-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="89b31-338">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="89b31-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="89b31-339">:::no-loc(Delete)::: o banco de dados e usa migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89b31-339">:::no-loc(Delete)::: the database and use migrations to re-create the database.</span></span> <span data-ttu-id="89b31-340">Para excluir o banco de dados, exclua o arquivo de banco de dados ( *MvcMovie.db* ).</span><span class="sxs-lookup"><span data-stu-id="89b31-340">To delete the database, delete the database file ( *MvcMovie.db* ).</span></span> <span data-ttu-id="89b31-341">Em seguida, execute o comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="89b31-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="89b31-342">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="89b31-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="89b31-343">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="89b31-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89b31-344">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="89b31-344">Additional resources</span></span>

* [<span data-ttu-id="89b31-345">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="89b31-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="89b31-346">[Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="89b31-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
