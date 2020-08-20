---
title: Parte 4, com um banco de dados e ASP.NET Core
author: rick-anderson
description: Parte 4 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 7/22/2019
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: d2e18782411b1801c74fa33ba1b31bad9662f3b2
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627111"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="f446b-103">Parte 4, com um banco de dados e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f446b-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="f446b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="f446b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f446b-105">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f446b-106">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f446b-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f446b-108">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="f446b-109">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f446b-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f446b-110">Para o desenvolvimento local, ele obtém a cadeia de conexão do *appsettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="f446b-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f446b-112">O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado.</span><span class="sxs-lookup"><span data-stu-id="f446b-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="f446b-113">O valor do nome é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="f446b-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f446b-114">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="f446b-115">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real.</span><span class="sxs-lookup"><span data-stu-id="f446b-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="f446b-116">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f446b-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f446b-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f446b-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f446b-119">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="f446b-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="f446b-120">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="f446b-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f446b-121">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="f446b-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="f446b-122">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="f446b-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="f446b-124">Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:</span><span class="sxs-lookup"><span data-stu-id="f446b-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menus contextuais abertos na tabela Movie](sql/_static/design.png)

  ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

<span data-ttu-id="f446b-127">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="f446b-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f446b-128">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f446b-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="f446b-129">Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:</span><span class="sxs-lookup"><span data-stu-id="f446b-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f446b-131">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="f446b-132">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f446b-132">Seed the database</span></span>

<span data-ttu-id="f446b-133">Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f446b-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f446b-134">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="f446b-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f446b-135">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="f446b-135">Add the seed initializer</span></span>

<span data-ttu-id="f446b-136">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f446b-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f446b-137">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="f446b-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f446b-138">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="f446b-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="f446b-139">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="f446b-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="f446b-140">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="f446b-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="f446b-141">A seguinte exceção ocorre quando `Update-Database` não foi executada:</span><span class="sxs-lookup"><span data-stu-id="f446b-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="f446b-142">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f446b-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f446b-144">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="f446b-144">Delete all the records in the DB.</span></span> <span data-ttu-id="f446b-145">Você pode fazer isso com os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="f446b-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="f446b-146">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="f446b-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f446b-147">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f446b-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f446b-148">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f446b-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f446b-149">Clique com o botão direito do mouse no ícone da bandeja do sistema IIS Express na área de notificação e toque em **sair** ou **parar site**:</span><span class="sxs-lookup"><span data-stu-id="f446b-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="f446b-152">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="f446b-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="f446b-153">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.</span><span class="sxs-lookup"><span data-stu-id="f446b-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f446b-154">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f446b-155">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="f446b-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f446b-156">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="f446b-157">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f446b-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="f446b-158">O próximo tutorial melhorará a apresentação dos dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f446b-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f446b-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f446b-160">[Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizando as páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="f446b-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f446b-161">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f446b-162">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f446b-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f446b-164">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="f446b-165">Para obter mais informações sobre os métodos usados em `ConfigureServices`, veja:</span><span class="sxs-lookup"><span data-stu-id="f446b-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="f446b-166">[Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) da UE no ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="f446b-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="f446b-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="f446b-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="f446b-168">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f446b-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f446b-169">Para o desenvolvimento local, ele obtém a cadeia de conexão do *appsettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="f446b-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f446b-171">O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado.</span><span class="sxs-lookup"><span data-stu-id="f446b-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="f446b-172">O valor do nome é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="f446b-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f446b-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f446b-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f446b-174">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="f446b-175">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real.</span><span class="sxs-lookup"><span data-stu-id="f446b-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="f446b-176">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f446b-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f446b-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f446b-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f446b-179">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="f446b-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="f446b-180">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="f446b-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f446b-181">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="f446b-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="f446b-182">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="f446b-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="f446b-184">Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:</span><span class="sxs-lookup"><span data-stu-id="f446b-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu contextual aberto na tabela Movie](sql/_static/design.png)

  ![Tabela Movie aberta no Designer](sql/_static/dv.png)

<span data-ttu-id="f446b-187">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="f446b-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f446b-188">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f446b-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="f446b-189">Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:</span><span class="sxs-lookup"><span data-stu-id="f446b-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f446b-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f446b-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f446b-192">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="f446b-193">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f446b-193">Seed the database</span></span>

<span data-ttu-id="f446b-194">Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f446b-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f446b-195">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="f446b-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f446b-196">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="f446b-196">Add the seed initializer</span></span>

<span data-ttu-id="f446b-197">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f446b-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f446b-198">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="f446b-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f446b-199">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="f446b-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="f446b-200">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="f446b-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="f446b-201">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="f446b-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="f446b-202">Um aplicativo de produção não chamaria `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="f446b-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="f446b-203">Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:</span><span class="sxs-lookup"><span data-stu-id="f446b-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="f446b-204">SqlException: não é possível abrir Razor o banco de dados "PagesMovieContext-21" solicitado pelo logon.</span><span class="sxs-lookup"><span data-stu-id="f446b-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="f446b-205">Falha no logon.</span><span class="sxs-lookup"><span data-stu-id="f446b-205">The login failed.</span></span>
<span data-ttu-id="f446b-206">O logon falhou para o usuário 'user name'.</span><span class="sxs-lookup"><span data-stu-id="f446b-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f446b-207">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f446b-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f446b-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f446b-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f446b-209">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="f446b-209">Delete all the records in the DB.</span></span> <span data-ttu-id="f446b-210">Você pode fazer isso com os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="f446b-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="f446b-211">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="f446b-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f446b-212">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f446b-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f446b-213">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f446b-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f446b-214">Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site**:</span><span class="sxs-lookup"><span data-stu-id="f446b-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="f446b-217">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="f446b-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="f446b-218">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.</span><span class="sxs-lookup"><span data-stu-id="f446b-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f446b-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f446b-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f446b-220">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="f446b-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f446b-221">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="f446b-222">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f446b-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f446b-223">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f446b-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f446b-224">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="f446b-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f446b-225">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="f446b-226">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f446b-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="f446b-227">O aplicativo mostra os dados propagados:</span><span class="sxs-lookup"><span data-stu-id="f446b-227">The app shows the seeded data:</span></span>

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55.png)

<span data-ttu-id="f446b-229">O próximo tutorial limpará a apresentação dos dados.</span><span class="sxs-lookup"><span data-stu-id="f446b-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f446b-230">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f446b-230">Additional resources</span></span>

* [<span data-ttu-id="f446b-231">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="f446b-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="f446b-232">[Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizando as páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="f446b-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
