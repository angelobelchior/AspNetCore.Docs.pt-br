---
title: Parte 4, trabalhar com um banco de dados
author: rick-anderson
description: 'Parte 4 da série de tutoriais em Razor páginas.'
ms.author: riande
ms.date: 09/26/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e78b5b6dab7145413ae8612bfeb352f328ec86a
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360705"
---
# <a name="part-4-work-with-a-database-and-aspnet-core"></a><span data-ttu-id="44ba8-103">Parte 4, trabalhar com um banco de dados e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44ba8-103">Part 4, work with a database and ASP.NET Core</span></span>

<span data-ttu-id="44ba8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="44ba8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="44ba8-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="44ba8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="44ba8-106">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44ba8-107">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="44ba8-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-109">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="44ba8-110">O sistema de [configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString` chave.</span><span class="sxs-lookup"><span data-stu-id="44ba8-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="44ba8-111">Para o desenvolvimento local, a configuração Obtém a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="44ba8-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44ba8-113">A cadeia de conexão gerada será semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="44ba8-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-114">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="44ba8-115">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão para um servidor de banco de dados de teste ou de produção.</span><span class="sxs-lookup"><span data-stu-id="44ba8-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="44ba8-116">Para obter mais informações, consulte [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="44ba8-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44ba8-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44ba8-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44ba8-119">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="44ba8-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="44ba8-120">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="44ba8-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44ba8-121">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="44ba8-122">No menu **Exibir** , abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="44ba8-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menu de exibição](sql/_static/5/ssox.png)

1. <span data-ttu-id="44ba8-124">Clique com o botão direito do mouse na `Movie` tabela e selecione **Exibir Designer** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-124">Right-click on the `Movie` table and select **View Designer** :</span></span>

   ![Menus contextuais abertos na tabela Movie](sql/_static/5/design.png)

   ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

   <span data-ttu-id="44ba8-127">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44ba8-128">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="44ba8-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="44ba8-129">Clique com o botão direito do mouse na `Movie` tabela e selecione **exibir dados** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-129">Right-click on the `Movie` table and select **View Data** :</span></span>

   ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-131">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="44ba8-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="44ba8-132">SQLite</span></span>

<span data-ttu-id="44ba8-133">O site do [SQLite](https://www.sqlite.org/) afirma:</span><span class="sxs-lookup"><span data-stu-id="44ba8-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="44ba8-134">O SQLite é um mecanismo de banco de dados SQL independente, de alta confiabilidade, inserido, completo e de domínio público.</span><span class="sxs-lookup"><span data-stu-id="44ba8-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="44ba8-135">O SQLite é o mecanismo de banco de dados mais usado no mundo.</span><span class="sxs-lookup"><span data-stu-id="44ba8-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="44ba8-136">Há muitas ferramentas de terceiros que você pode baixar para gerenciar e exibir um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="44ba8-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="44ba8-137">A imagem abaixo é do [Navegador do DB para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="44ba8-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="44ba8-138">Se você tiver uma ferramenta favorita do SQLite, deixe um comentário sobre o que mais gosta dela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Navegador de BD para SQLite mostrando banco de dados de filmes](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="44ba8-140">Para este tutorial, o recurso de *migrações* Entity Framework Core é usado sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="44ba8-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="44ba8-141">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="44ba8-142">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="44ba8-143">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="44ba8-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="44ba8-144">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="44ba8-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="44ba8-145">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="44ba8-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="44ba8-146">Em vez disso, quando o esquema é alterado, o banco de dados é Descartado e recriado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="44ba8-147">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="44ba8-148">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="44ba8-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="44ba8-149">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-149">Creating a new table.</span></span>
>* <span data-ttu-id="44ba8-150">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="44ba8-151">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="44ba8-151">Dropping the old table.</span></span>
>* <span data-ttu-id="44ba8-152">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-152">Renaming the new table.</span></span>
>
><span data-ttu-id="44ba8-153">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="44ba8-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="44ba8-154">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="44ba8-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="44ba8-155">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="44ba8-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="44ba8-156">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="44ba8-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="44ba8-157">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="44ba8-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="44ba8-158">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="44ba8-158">Seed the database</span></span>

<span data-ttu-id="44ba8-159">Create uma nova classe denominada `SeedData` na pasta *modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="44ba8-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44ba8-160">Se houver filmes no banco de dados, o inicializador de semente retornará e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44ba8-161">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="44ba8-161">Add the seed initializer</span></span>

<span data-ttu-id="44ba8-162">Substitua o conteúdo do *Program.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="44ba8-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="44ba8-163">No código anterior, o `Main` método foi modificado para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="44ba8-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="44ba8-164">Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="44ba8-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="44ba8-165">Chame o `seedData.Initialize` método, passando a ele a instância de contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="44ba8-166">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="44ba8-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="44ba8-167">A [instrução using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garante que o contexto seja Descartado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="44ba8-168">A seguinte exceção ocorre quando `Update-Database` não foi executada:</span><span class="sxs-lookup"><span data-stu-id="44ba8-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="44ba8-169">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="44ba8-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="44ba8-171">Delete todos os registros no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-171">Delete all the records in the database.</span></span> <span data-ttu-id="44ba8-172">Usar os links de exclusão no navegador ou do [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="44ba8-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="44ba8-173">Force o aplicativo a ser inicializado chamando os métodos na `Startup` classe, para que o método semente seja executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="44ba8-174">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44ba8-175">Pare e reinicie o IIS com qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="44ba8-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="44ba8-176">Clique com o botão direito do mouse no ícone da bandeja do sistema IIS Express na área de notificação e selecione **sair** ou **parar site** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site** :</span></span>

      ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu contextual](sql/_static/stopIIS.png)

   1. <span data-ttu-id="44ba8-179">Se o aplicativo estiver sendo executado no modo de não depuração, pressione <kbd>F5</kbd> para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="44ba8-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="44ba8-180">Se o aplicativo estiver no modo de depuração, pare o depurador e pressione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="44ba8-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-181">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="44ba8-182">Delete todos os registros no banco de dados, portanto, o método de semente será executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44ba8-183">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44ba8-184">O aplicativo mostra os dados propagados:</span><span class="sxs-lookup"><span data-stu-id="44ba8-184">The app shows the seeded data:</span></span>

![Aplicativo de filme aberto no navegador mostrando dados de filmes](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="44ba8-186">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="44ba8-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="44ba8-187">[Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizar as páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="44ba8-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="44ba8-188">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="44ba8-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="44ba8-189">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44ba8-190">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="44ba8-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-192">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="44ba8-193">O sistema de [configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString` chave.</span><span class="sxs-lookup"><span data-stu-id="44ba8-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="44ba8-194">Para o desenvolvimento local, a configuração Obtém a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="44ba8-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44ba8-196">A cadeia de conexão gerada será semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="44ba8-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-197">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="44ba8-198">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão para um servidor de banco de dados de teste ou de produção.</span><span class="sxs-lookup"><span data-stu-id="44ba8-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="44ba8-199">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="44ba8-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44ba8-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44ba8-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44ba8-202">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="44ba8-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="44ba8-203">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="44ba8-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44ba8-204">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="44ba8-205">No menu **Exibir** , abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="44ba8-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="44ba8-207">Clique com o botão direito do mouse na `Movie` tabela e selecione **Exibir Designer** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-207">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![Menus contextuais abertos na tabela Movie](sql/_static/design.png)

  ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

<span data-ttu-id="44ba8-210">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44ba8-211">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="44ba8-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="44ba8-212">Clique com o botão direito do mouse na `Movie` tabela e selecione **exibir dados** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-212">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-214">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="44ba8-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="44ba8-215">SQLite</span></span>

<span data-ttu-id="44ba8-216">O site do [SQLite](https://www.sqlite.org/) afirma:</span><span class="sxs-lookup"><span data-stu-id="44ba8-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="44ba8-217">O SQLite é um mecanismo de banco de dados SQL independente, de alta confiabilidade, inserido, completo e de domínio público.</span><span class="sxs-lookup"><span data-stu-id="44ba8-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="44ba8-218">O SQLite é o mecanismo de banco de dados mais usado no mundo.</span><span class="sxs-lookup"><span data-stu-id="44ba8-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="44ba8-219">Há muitas ferramentas de terceiros que você pode baixar para gerenciar e exibir um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="44ba8-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="44ba8-220">A imagem abaixo é do [Navegador do DB para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="44ba8-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="44ba8-221">Se você tiver uma ferramenta favorita do SQLite, deixe um comentário sobre o que mais gosta dela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Navegador de BD para SQLite mostrando banco de dados de filmes](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="44ba8-223">Para este tutorial, o recurso de *migrações* Entity Framework Core é usado sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="44ba8-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="44ba8-224">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="44ba8-225">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="44ba8-226">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="44ba8-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="44ba8-227">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="44ba8-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="44ba8-228">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="44ba8-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="44ba8-229">Em vez disso, quando o esquema é alterado, o banco de dados é Descartado e recriado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="44ba8-230">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="44ba8-231">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="44ba8-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="44ba8-232">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-232">Creating a new table.</span></span>
>* <span data-ttu-id="44ba8-233">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="44ba8-234">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="44ba8-234">Dropping the old table.</span></span>
>* <span data-ttu-id="44ba8-235">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="44ba8-235">Renaming the new table.</span></span>
>
><span data-ttu-id="44ba8-236">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="44ba8-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="44ba8-237">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="44ba8-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="44ba8-238">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="44ba8-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="44ba8-239">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="44ba8-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="44ba8-240">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="44ba8-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="44ba8-241">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="44ba8-241">Seed the database</span></span>

<span data-ttu-id="44ba8-242">Create uma nova classe denominada `SeedData` na pasta *modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="44ba8-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44ba8-243">Se houver filmes no banco de dados, o inicializador de semente retornará e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44ba8-244">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="44ba8-244">Add the seed initializer</span></span>

<span data-ttu-id="44ba8-245">Substitua o conteúdo do *Program.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="44ba8-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="44ba8-246">No código anterior, o `Main` método foi modificado para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="44ba8-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="44ba8-247">Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="44ba8-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="44ba8-248">Chame o `seedData.Initialize` método, passando a ele a instância de contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="44ba8-249">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="44ba8-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="44ba8-250">A [instrução using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garante que o contexto seja Descartado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="44ba8-251">A seguinte exceção ocorre quando `Update-Database` não foi executada:</span><span class="sxs-lookup"><span data-stu-id="44ba8-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="44ba8-252">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="44ba8-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44ba8-254">Delete todos os registros no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-254">Delete all the records in the database.</span></span> <span data-ttu-id="44ba8-255">Use os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="44ba8-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="44ba8-256">Force o aplicativo a ser inicializado chamando os métodos na `Startup` classe, para que o método semente seja executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="44ba8-257">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44ba8-258">Pare e reinicie o IIS com qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="44ba8-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="44ba8-259">Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="44ba8-262">Se o aplicativo estiver sendo executado no modo de não depuração, pressione <kbd>F5</kbd> para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="44ba8-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="44ba8-263">Se o aplicativo estiver no modo de depuração, pare o depurador e pressione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="44ba8-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-264">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="44ba8-265">Delete todos os registros no banco de dados, portanto, o método de semente será executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44ba8-266">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44ba8-267">O aplicativo mostra os dados propagados:</span><span class="sxs-lookup"><span data-stu-id="44ba8-267">The app shows the seeded data:</span></span>

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="44ba8-269">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="44ba8-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="44ba8-270">[Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizar as páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="44ba8-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44ba8-271">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="44ba8-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="44ba8-272">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44ba8-273">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="44ba8-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-275">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="44ba8-276">Para obter mais informações sobre os métodos usados em `ConfigureServices`, veja:</span><span class="sxs-lookup"><span data-stu-id="44ba8-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="44ba8-277">[Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) da UE no ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="44ba8-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="44ba8-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="44ba8-279">O sistema de [configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString` chave.</span><span class="sxs-lookup"><span data-stu-id="44ba8-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="44ba8-280">Para o desenvolvimento local, a configuração Obtém a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="44ba8-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44ba8-282">A cadeia de conexão gerada será semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="44ba8-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="44ba8-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44ba8-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-284">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="44ba8-285">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão para um servidor de banco de dados de teste ou de produção.</span><span class="sxs-lookup"><span data-stu-id="44ba8-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="44ba8-286">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="44ba8-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44ba8-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44ba8-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44ba8-289">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="44ba8-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="44ba8-290">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="44ba8-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44ba8-291">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="44ba8-292">No menu **Exibir** , abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="44ba8-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="44ba8-294">Clique com o botão direito do mouse na `Movie` tabela e selecione **Exibir Designer** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-294">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![Menu contextual aberto na tabela Movie](sql/_static/design.png)

  ![Tabela Movie aberta no Designer](sql/_static/dv.png)

<span data-ttu-id="44ba8-297">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44ba8-298">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="44ba8-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="44ba8-299">Clique com o botão direito do mouse na `Movie` tabela e selecione **exibir dados** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-299">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="44ba8-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44ba8-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-302">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="44ba8-303">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="44ba8-303">Seed the database</span></span>

<span data-ttu-id="44ba8-304">Create uma nova classe denominada `SeedData` na pasta *modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="44ba8-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44ba8-305">Se houver filmes no banco de dados, o inicializador de semente retornará e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44ba8-306">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="44ba8-306">Add the seed initializer</span></span>

<span data-ttu-id="44ba8-307">Substitua o conteúdo do *Program.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="44ba8-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="44ba8-308">No código anterior, o `Main` método foi modificado para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="44ba8-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="44ba8-309">Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="44ba8-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="44ba8-310">Chame o `seedData.Initialize` método, passando a ele a instância de contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="44ba8-311">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="44ba8-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="44ba8-312">A [instrução using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garante que o contexto seja Descartado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="44ba8-313">Um aplicativo de produção não chamaria `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="44ba8-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="44ba8-314">Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:</span><span class="sxs-lookup"><span data-stu-id="44ba8-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="44ba8-315">SqlException: não é possível abrir Razor o banco de dados "PagesMovieContext-21" solicitado pelo logon.</span><span class="sxs-lookup"><span data-stu-id="44ba8-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="44ba8-316">Falha no logon.</span><span class="sxs-lookup"><span data-stu-id="44ba8-316">The login failed.</span></span>
<span data-ttu-id="44ba8-317">O logon falhou para o usuário 'user name'.</span><span class="sxs-lookup"><span data-stu-id="44ba8-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="44ba8-318">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="44ba8-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44ba8-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44ba8-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44ba8-320">Delete todos os registros no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-320">Delete all the records in the database.</span></span> <span data-ttu-id="44ba8-321">Você pode fazer isso com os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="44ba8-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="44ba8-322">Force o aplicativo a ser inicializado chamando os métodos na `Startup` classe, para que o método semente seja executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="44ba8-323">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44ba8-324">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="44ba8-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="44ba8-325">Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site** :</span><span class="sxs-lookup"><span data-stu-id="44ba8-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="44ba8-328">Se o aplicativo estiver sendo executado no modo de não depuração, pressione <kbd>F5</kbd> para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="44ba8-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="44ba8-329">Se o aplicativo estiver no modo de depuração, pare o depurador e pressione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="44ba8-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44ba8-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44ba8-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44ba8-331">Delete todos os registros no banco de dados, portanto, o método de semente será executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44ba8-332">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44ba8-333">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44ba8-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="44ba8-334">Delete todos os registros no banco de dados, portanto, o método de semente será executado.</span><span class="sxs-lookup"><span data-stu-id="44ba8-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44ba8-335">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44ba8-336">O aplicativo mostra os dados propagados:</span><span class="sxs-lookup"><span data-stu-id="44ba8-336">The app shows the seeded data:</span></span>

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55https.png)

<span data-ttu-id="44ba8-338">O próximo tutorial limpará a apresentação dos dados.</span><span class="sxs-lookup"><span data-stu-id="44ba8-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44ba8-339">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="44ba8-339">Additional resources</span></span>

* [<span data-ttu-id="44ba8-340">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="44ba8-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="44ba8-341">[Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizar as páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="44ba8-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
