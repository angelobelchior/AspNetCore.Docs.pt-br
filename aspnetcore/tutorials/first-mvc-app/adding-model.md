---
title: Parte 4, adicionar um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 4 da série de tutoriais no ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688443"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="6805a-103">Parte 4, adicionar um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6805a-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="6805a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="6805a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="6805a-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="6805a-106">Essas classes serão a parte “**M** odel” parte do aplicativo **M** VC.</span><span class="sxs-lookup"><span data-stu-id="6805a-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="6805a-107">Você usa essas classes com o [Entity Framework Core](/ef/core) (EF Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="6805a-108">O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="6805a-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="6805a-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o** bjetos **C** L **R\*\*\*\*b** ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="6805a-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6805a-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="6805a-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6805a-112">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-114">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6805a-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6805a-115">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6805a-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6805a-117">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6805a-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6805a-119">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar**  >  **nova** classe  >  **vazia classe**.</span><span class="sxs-lookup"><span data-stu-id="6805a-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="6805a-120">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6805a-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="6805a-121">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6805a-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="6805a-122">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="6805a-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="6805a-123">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o tipo de dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="6805a-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6805a-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="6805a-124">With this attribute:</span></span>

* <span data-ttu-id="6805a-125">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="6805a-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="6805a-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="6805a-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6805a-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6805a-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="6805a-128">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="6805a-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-130">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6805a-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6805a-132">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="6805a-133">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="6805a-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="6805a-134">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="6805a-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="6805a-135">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6805a-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6805a-138">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6805a-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="6805a-139">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="6805a-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="6805a-140">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="6805a-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="6805a-142">A caixa de diálogo **selecionar projetos** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="6805a-143">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="6805a-143">Press the **Ok** button.</span></span>

<span data-ttu-id="6805a-144">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="6805a-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="6805a-145">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="6805a-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="6805a-146">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="6805a-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="6805a-147">Execute o seguinte comando da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="6805a-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="6805a-148">O comando anterior adiciona a [ferramenta ASPNET-CodeGenerator scaffolding](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="6805a-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="6805a-149">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-149">Create a database context class</span></span>

<span data-ttu-id="6805a-150">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="6805a-151">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="6805a-152">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="6805a-152">Create a *Data* folder.</span></span>

<span data-ttu-id="6805a-153">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6805a-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="6805a-154">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6805a-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6805a-155">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6805a-156">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6805a-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="6805a-157">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-157">Register the database context</span></span>

<span data-ttu-id="6805a-158">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6805a-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6805a-159">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6805a-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="6805a-160">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6805a-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6805a-161">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6805a-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="6805a-162">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="6805a-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="6805a-163">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6805a-164">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6805a-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="6805a-167">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6805a-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6805a-168">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="6805a-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="6805a-169">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-169">Add a database connection string</span></span>

<span data-ttu-id="6805a-170">Adicione uma cadeia de conexão ao *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="6805a-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-172">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="6805a-173">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="6805a-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="6805a-174">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="6805a-174">Scaffold movie pages</span></span>

<span data-ttu-id="6805a-175">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6805a-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-177">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6805a-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="6805a-179">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6805a-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6805a-181">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="6805a-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6805a-182">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="6805a-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6805a-183">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="6805a-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc5.png)

* <span data-ttu-id="6805a-185">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="6805a-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6805a-186">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="6805a-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6805a-187">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="6805a-187">Select **Add**</span></span>

<span data-ttu-id="6805a-188">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="6805a-188">Visual Studio creates:</span></span>

* <span data-ttu-id="6805a-189">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6805a-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6805a-190">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas (*exibições/filmes/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6805a-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6805a-191">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6805a-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="6805a-193">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6805a-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6805a-194">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="6805a-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6805a-195">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6805a-197">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6805a-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6805a-198">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="6805a-199">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="6805a-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="6805a-200">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="6805a-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="6805a-201">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6805a-201">Initial migration</span></span>

<span data-ttu-id="6805a-202">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="6805a-203">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-205">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6805a-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="6805a-206">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6805a-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="6805a-207">`Add-Migration InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6805a-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6805a-208">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6805a-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6805a-209">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6805a-210">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6805a-211">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6805a-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="6805a-212">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="6805a-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6805a-213">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="6805a-214">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="6805a-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="6805a-215">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="6805a-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6805a-216">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="6805a-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6805a-217">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="6805a-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="6805a-218">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6805a-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-219">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="6805a-220">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="6805a-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="6805a-221">`ef migrations add InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6805a-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6805a-222">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6805a-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6805a-223">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6805a-224">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6805a-225">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6805a-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="6805a-226">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="6805a-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6805a-227">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="6805a-228">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="6805a-228">The InitialCreate class</span></span>

<span data-ttu-id="6805a-229">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="6805a-230">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="6805a-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="6805a-231">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="6805a-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="6805a-232">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6805a-232">Test the app</span></span>

* <span data-ttu-id="6805a-233">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="6805a-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="6805a-234">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="6805a-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-236">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="6805a-237">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="6805a-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="6805a-238">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6805a-238">Test the **Create** page.</span></span> <span data-ttu-id="6805a-239">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6805a-240">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6805a-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6805a-241">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6805a-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6805a-242">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6805a-242">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6805a-243">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6805a-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="6805a-244">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="6805a-244">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-246">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="6805a-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6805a-247">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6805a-248">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6805a-250">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6805a-251">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="6805a-252">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="6805a-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="6805a-253">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6805a-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="6805a-254">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="6805a-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="6805a-255">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="6805a-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6805a-256">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="6805a-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6805a-257">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6805a-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6805a-258">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6805a-259">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6805a-260">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="6805a-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="6805a-261">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="6805a-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="6805a-262">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6805a-263">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="6805a-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6805a-264">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="6805a-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6805a-265">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6805a-266">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6805a-267">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6805a-268">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6805a-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6805a-269">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="6805a-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6805a-270">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="6805a-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6805a-271">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="6805a-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="6805a-272">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6805a-273">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="6805a-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="6805a-274">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="6805a-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="6805a-275">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="6805a-276">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="6805a-277">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6805a-278">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6805a-279">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="6805a-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6805a-280">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="6805a-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6805a-281">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="6805a-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6805a-282">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6805a-283">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6805a-284">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="6805a-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6805a-285">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6805a-286">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="6805a-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6805a-287">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6805a-287">Additional resources</span></span>

* [<span data-ttu-id="6805a-288">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="6805a-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6805a-289">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="6805a-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6805a-290">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6805a-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6805a-291">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-291">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-293">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6805a-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6805a-294">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6805a-294">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6805a-296">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6805a-296">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-297">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-297">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6805a-298">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar**  >  **nova** classe  >  **vazia classe**.</span><span class="sxs-lookup"><span data-stu-id="6805a-298">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="6805a-299">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6805a-299">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="6805a-300">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6805a-300">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="6805a-301">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="6805a-301">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="6805a-302">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o tipo de dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="6805a-302">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6805a-303">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="6805a-303">With this attribute:</span></span>

* <span data-ttu-id="6805a-304">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="6805a-304">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="6805a-305">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="6805a-305">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6805a-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6805a-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="6805a-307">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="6805a-307">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-308">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-309">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6805a-309">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6805a-311">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-311">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="6805a-312">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="6805a-312">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="6805a-313">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="6805a-313">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="6805a-314">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6805a-314">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-316">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6805a-317">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6805a-317">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="6805a-318">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="6805a-318">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="6805a-319">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="6805a-319">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="6805a-321">A caixa de diálogo **selecionar projetos** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-321">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="6805a-322">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="6805a-322">Press the **Ok** button.</span></span>

<span data-ttu-id="6805a-323">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="6805a-323">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="6805a-324">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="6805a-324">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="6805a-325">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="6805a-325">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="6805a-326">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-326">Create a database context class</span></span>

<span data-ttu-id="6805a-327">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-327">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="6805a-328">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-328">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="6805a-329">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="6805a-329">Create a *Data* folder.</span></span>

<span data-ttu-id="6805a-330">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6805a-330">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="6805a-331">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6805a-331">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6805a-332">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-332">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6805a-333">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6805a-333">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="6805a-334">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-334">Register the database context</span></span>

<span data-ttu-id="6805a-335">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6805a-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6805a-336">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6805a-336">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="6805a-337">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6805a-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6805a-338">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6805a-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="6805a-339">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="6805a-339">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="6805a-340">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-340">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6805a-341">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6805a-341">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-342">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-342">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-343">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-343">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="6805a-344">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6805a-344">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6805a-345">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="6805a-345">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="6805a-346">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-346">Add a database connection string</span></span>

<span data-ttu-id="6805a-347">Adicione uma cadeia de conexão ao *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="6805a-347">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-348">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-349">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-349">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="6805a-350">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="6805a-350">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="6805a-351">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="6805a-351">Scaffold movie pages</span></span>

<span data-ttu-id="6805a-352">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6805a-352">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-353">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-353">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-354">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6805a-354">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="6805a-356">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6805a-356">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6805a-358">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="6805a-358">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6805a-359">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="6805a-359">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6805a-360">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="6805a-360">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* <span data-ttu-id="6805a-362">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="6805a-362">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6805a-363">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="6805a-363">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6805a-364">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="6805a-364">Select **Add**</span></span>

<span data-ttu-id="6805a-365">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="6805a-365">Visual Studio creates:</span></span>

* <span data-ttu-id="6805a-366">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6805a-366">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6805a-367">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas (*exibições/filmes/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6805a-367">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6805a-368">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6805a-368">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-369">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-369">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="6805a-370">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6805a-370">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6805a-371">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="6805a-371">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6805a-372">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-372">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-373">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-373">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6805a-374">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6805a-374">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6805a-375">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-375">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="6805a-376">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="6805a-376">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="6805a-377">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="6805a-377">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="6805a-378">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6805a-378">Initial migration</span></span>

<span data-ttu-id="6805a-379">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-379">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="6805a-380">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-380">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-381">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-382">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6805a-382">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="6805a-383">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6805a-383">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="6805a-384">`Add-Migration InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6805a-384">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6805a-385">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6805a-385">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6805a-386">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-386">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6805a-387">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-387">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6805a-388">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6805a-388">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="6805a-389">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="6805a-389">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6805a-390">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-390">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="6805a-391">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="6805a-391">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="6805a-392">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="6805a-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6805a-393">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="6805a-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6805a-394">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="6805a-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="6805a-395">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6805a-395">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-396">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-396">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="6805a-397">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="6805a-397">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="6805a-398">`ef migrations add InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6805a-398">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6805a-399">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6805a-399">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6805a-400">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-400">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6805a-401">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-401">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6805a-402">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6805a-402">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="6805a-403">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="6805a-403">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6805a-404">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-404">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="6805a-405">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="6805a-405">The InitialCreate class</span></span>

<span data-ttu-id="6805a-406">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-406">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="6805a-407">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="6805a-407">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="6805a-408">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="6805a-408">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="6805a-409">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6805a-409">Test the app</span></span>

* <span data-ttu-id="6805a-410">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="6805a-410">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="6805a-411">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="6805a-411">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-412">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-413">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-413">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="6805a-414">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="6805a-414">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="6805a-415">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6805a-415">Test the **Create** page.</span></span> <span data-ttu-id="6805a-416">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-416">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6805a-417">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6805a-417">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6805a-418">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6805a-418">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6805a-419">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6805a-419">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6805a-420">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6805a-420">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="6805a-421">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="6805a-421">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-423">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="6805a-423">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6805a-424">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-424">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6805a-425">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-425">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-426">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-426">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6805a-427">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-427">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6805a-428">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-428">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="6805a-429">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="6805a-429">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="6805a-430">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6805a-430">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="6805a-431">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="6805a-431">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="6805a-432">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="6805a-432">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6805a-433">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="6805a-433">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6805a-434">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6805a-434">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6805a-435">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-435">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6805a-436">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-436">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6805a-437">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="6805a-437">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="6805a-438">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="6805a-438">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="6805a-439">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-439">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6805a-440">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="6805a-440">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6805a-441">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="6805a-441">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6805a-442">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-442">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6805a-443">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-443">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6805a-444">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-444">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6805a-445">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6805a-445">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6805a-446">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="6805a-446">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6805a-447">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="6805a-447">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6805a-448">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="6805a-448">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="6805a-449">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-449">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6805a-450">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="6805a-450">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="6805a-451">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="6805a-451">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="6805a-452">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-452">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="6805a-453">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-453">The `Model` object is strongly typed.</span></span> <span data-ttu-id="6805a-454">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-454">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6805a-455">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-455">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6805a-456">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="6805a-456">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6805a-457">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="6805a-457">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6805a-458">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="6805a-458">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6805a-459">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-459">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6805a-460">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-460">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6805a-461">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="6805a-461">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6805a-462">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-462">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6805a-463">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="6805a-463">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6805a-464">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6805a-464">Additional resources</span></span>

* [<span data-ttu-id="6805a-465">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="6805a-465">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6805a-466">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="6805a-466">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6805a-467">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6805a-467">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6805a-468">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6805a-468">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-470">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6805a-470">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6805a-471">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="6805a-471">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-472">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6805a-473">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6805a-473">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6805a-474">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="6805a-474">Scaffold the movie model</span></span>

<span data-ttu-id="6805a-475">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6805a-475">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6805a-476">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="6805a-476">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-477">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-477">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-478">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6805a-478">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="6805a-480">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6805a-480">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6805a-482">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="6805a-482">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6805a-483">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="6805a-483">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6805a-484">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="6805a-484">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="6805a-486">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="6805a-486">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6805a-487">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="6805a-487">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6805a-488">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="6805a-488">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="6805a-490">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="6805a-490">Visual Studio creates:</span></span>

* <span data-ttu-id="6805a-491">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="6805a-491">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="6805a-492">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6805a-492">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6805a-493">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas (*exibições/filmes/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6805a-493">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6805a-494">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6805a-494">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6805a-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6805a-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6805a-496">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6805a-496">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6805a-497">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6805a-497">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6805a-498">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="6805a-498">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6805a-499">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-499">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6805a-500">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-500">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6805a-501">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6805a-501">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6805a-502">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6805a-502">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6805a-503">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6805a-503">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="6805a-504">Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6805a-504">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-505">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-506">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-506">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="6805a-507">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="6805a-507">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="6805a-508">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="6805a-508">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6805a-509">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6805a-509">Initial migration</span></span>

<span data-ttu-id="6805a-510">Nesta seção, há estas tarefas:</span><span class="sxs-lookup"><span data-stu-id="6805a-510">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="6805a-511">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6805a-511">Add an initial migration.</span></span>
* <span data-ttu-id="6805a-512">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6805a-512">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-513">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-513">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6805a-514">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6805a-514">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="6805a-516">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6805a-516">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="6805a-517">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="6805a-517">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="6805a-518">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6805a-518">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="6805a-519">O argumento `Initial` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6805a-519">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="6805a-520">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="6805a-520">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="6805a-521">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="6805a-521">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="6805a-522">O `Update-Database` comando executa o `Up` método no arquivo *Migrations/{time-Stamp} _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-522">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-523">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-523">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="6805a-524">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="6805a-524">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="6805a-525">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6805a-525">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="6805a-526">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6805a-526">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6805a-527">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6805a-527">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6805a-528">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6805a-528">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6805a-529">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6805a-529">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6805a-530">Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6805a-530">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="6805a-531">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6805a-531">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6805a-532">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6805a-532">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6805a-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6805a-533">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6805a-534">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="6805a-534">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="6805a-535">Examine o seguinte método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6805a-535">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6805a-536">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="6805a-536">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="6805a-537">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-537">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="6805a-538">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6805a-538">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6805a-539">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="6805a-539">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="6805a-540">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6805a-540">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6805a-541">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-541">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6805a-542">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6805a-542">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6805a-543">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6805a-543">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6805a-544">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="6805a-544">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6805a-545">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6805a-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6805a-546">Você criou um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="6805a-546">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6805a-547">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6805a-547">Test the app</span></span>

* <span data-ttu-id="6805a-548">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="6805a-548">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6805a-549">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6805a-549">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6805a-550">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="6805a-550">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6805a-551">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6805a-551">Test the **Create** link.</span></span> <span data-ttu-id="6805a-552">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="6805a-552">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6805a-553">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6805a-553">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6805a-554">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6805a-554">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6805a-555">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6805a-555">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6805a-556">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6805a-556">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6805a-557">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6805a-557">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="6805a-558">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="6805a-558">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="6805a-559">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="6805a-559">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="6805a-560">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="6805a-560">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="6805a-561">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="6805a-561">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6805a-562">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-562">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6805a-563">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6805a-563">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6805a-564">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="6805a-564">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6805a-565">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6805a-565">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6805a-566">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-566">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6805a-567">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-567">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6805a-568">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="6805a-568">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="6805a-569">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="6805a-569">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="6805a-570">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6805a-570">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6805a-571">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="6805a-571">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6805a-572">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="6805a-572">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6805a-573">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-573">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6805a-574">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-574">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6805a-575">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6805a-575">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6805a-576">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6805a-576">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6805a-577">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="6805a-577">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6805a-578">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="6805a-578">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6805a-579">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="6805a-579">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="6805a-580">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-580">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6805a-581">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="6805a-581">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="6805a-582">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-582">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="6805a-583">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-583">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6805a-584">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-584">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6805a-585">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-585">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6805a-586">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="6805a-586">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6805a-587">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="6805a-587">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6805a-588">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="6805a-588">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6805a-589">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6805a-589">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6805a-590">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6805a-590">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6805a-591">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="6805a-591">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6805a-592">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6805a-592">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6805a-593">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="6805a-593">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6805a-594">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6805a-594">Additional resources</span></span>

* [<span data-ttu-id="6805a-595">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="6805a-595">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6805a-596">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="6805a-596">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6805a-597">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com um banco de dados](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6805a-597">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
