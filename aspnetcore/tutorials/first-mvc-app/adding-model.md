---
title: Parte 4, adicionar um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 4 da série de tutoriais no ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422731"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="37bed-103">Parte 4, adicionar um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="37bed-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="37bed-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="37bed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="37bed-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="37bed-106">Essas classes serão a parte “ **M** odel” parte do aplicativo **M** VC.</span><span class="sxs-lookup"><span data-stu-id="37bed-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="37bed-107">Você usa essas classes com o [Entity Framework Core](/ef/core) (EF Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="37bed-108">O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="37bed-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="37bed-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o** bjetos **C** L **R\*\*\*\*b** ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="37bed-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="37bed-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="37bed-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="37bed-112">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-114">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="37bed-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="37bed-115">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="37bed-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="37bed-117">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="37bed-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="37bed-119">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar**  >  **nova** classe  >  **vazia classe**.</span><span class="sxs-lookup"><span data-stu-id="37bed-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="37bed-120">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="37bed-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="37bed-121">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="37bed-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="37bed-122">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="37bed-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="37bed-123">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o tipo de dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="37bed-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="37bed-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="37bed-124">With this attribute:</span></span>

* <span data-ttu-id="37bed-125">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="37bed-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="37bed-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="37bed-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="37bed-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="37bed-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="37bed-128">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="37bed-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-130">No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="37bed-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="37bed-132">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="37bed-133">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="37bed-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="37bed-134">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="37bed-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="37bed-135">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="37bed-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="37bed-138">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="37bed-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="37bed-139">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="37bed-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="37bed-140">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="37bed-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="37bed-142">A caixa de diálogo **selecionar projetos** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="37bed-143">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="37bed-143">Press the **Ok** button.</span></span>

<span data-ttu-id="37bed-144">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="37bed-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="37bed-145">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="37bed-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="37bed-146">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="37bed-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="37bed-147">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-147">Create a database context class</span></span>

<span data-ttu-id="37bed-148">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="37bed-149">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="37bed-150">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="37bed-150">Create a *Data* folder.</span></span>

<span data-ttu-id="37bed-151">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="37bed-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="37bed-152">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="37bed-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="37bed-153">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="37bed-154">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="37bed-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="37bed-155">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-155">Register the database context</span></span>

<span data-ttu-id="37bed-156">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="37bed-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="37bed-157">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37bed-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="37bed-158">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="37bed-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="37bed-159">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="37bed-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="37bed-160">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="37bed-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="37bed-161">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="37bed-162">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="37bed-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-164">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="37bed-165">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="37bed-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="37bed-166">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="37bed-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="37bed-167">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-167">Add a database connection string</span></span>

<span data-ttu-id="37bed-168">Adicione uma cadeia de conexão ao *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="37bed-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-170">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="37bed-171">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="37bed-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="37bed-172">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="37bed-172">Scaffold movie pages</span></span>

<span data-ttu-id="37bed-173">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="37bed-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-175">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="37bed-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="37bed-177">Na caixa de diálogo **Adicionar Scaffold** , selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="37bed-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="37bed-179">Preencha a caixa de diálogo **Adicionar Controlador** :</span><span class="sxs-lookup"><span data-stu-id="37bed-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="37bed-180">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="37bed-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="37bed-181">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="37bed-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc5.png)

* <span data-ttu-id="37bed-183">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="37bed-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="37bed-184">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="37bed-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="37bed-185">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="37bed-185">Select **Add**</span></span>

<span data-ttu-id="37bed-186">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="37bed-186">Visual Studio creates:</span></span>

* <span data-ttu-id="37bed-187">Um controlador de filmes ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="37bed-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="37bed-188">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas ( *exibições/filmes/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="37bed-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="37bed-189">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="37bed-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="37bed-191">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs* , *Startup.cs* e *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="37bed-192">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="37bed-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="37bed-193">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-194">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="37bed-195">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs* , *Startup.cs* e *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="37bed-196">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="37bed-197">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="37bed-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="37bed-198">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="37bed-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="37bed-199">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="37bed-199">Initial migration</span></span>

<span data-ttu-id="37bed-200">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="37bed-201">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-203">No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="37bed-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="37bed-204">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="37bed-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="37bed-205">`Add-Migration InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="37bed-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="37bed-206">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="37bed-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="37bed-207">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="37bed-208">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="37bed-209">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="37bed-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="37bed-210">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="37bed-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="37bed-211">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="37bed-212">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="37bed-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="37bed-213">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="37bed-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="37bed-214">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="37bed-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="37bed-215">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="37bed-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="37bed-216">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="37bed-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-217">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="37bed-218">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="37bed-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="37bed-219">`ef migrations add InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="37bed-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="37bed-220">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="37bed-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="37bed-221">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="37bed-222">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="37bed-223">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="37bed-224">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="37bed-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="37bed-225">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="37bed-226">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="37bed-226">The InitialCreate class</span></span>

<span data-ttu-id="37bed-227">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="37bed-228">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="37bed-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="37bed-229">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="37bed-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="37bed-230">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="37bed-230">Test the app</span></span>

* <span data-ttu-id="37bed-231">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="37bed-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="37bed-232">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="37bed-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-234">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="37bed-235">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="37bed-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="37bed-236">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="37bed-236">Test the **Create** page.</span></span> <span data-ttu-id="37bed-237">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="37bed-238">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="37bed-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="37bed-239">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="37bed-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="37bed-240">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="37bed-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="37bed-241">Teste os links **Editar** , **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="37bed-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="37bed-242">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="37bed-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-244">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="37bed-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="37bed-245">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="37bed-246">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-247">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="37bed-248">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="37bed-249">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="37bed-250">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="37bed-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="37bed-251">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="37bed-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="37bed-252">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="37bed-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="37bed-253">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="37bed-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="37bed-254">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="37bed-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="37bed-255">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="37bed-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="37bed-256">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="37bed-257">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="37bed-258">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="37bed-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="37bed-259">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="37bed-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="37bed-260">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="37bed-261">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="37bed-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="37bed-262">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="37bed-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="37bed-263">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="37bed-264">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="37bed-265">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="37bed-266">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="37bed-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="37bed-267">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="37bed-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="37bed-268">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="37bed-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="37bed-269">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="37bed-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="37bed-270">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="37bed-271">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="37bed-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="37bed-272">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="37bed-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="37bed-273">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="37bed-274">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="37bed-275">Por exemplo, na exibição *Details.cshtml* , o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="37bed-276">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="37bed-277">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="37bed-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="37bed-278">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="37bed-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="37bed-279">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="37bed-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="37bed-280">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="37bed-281">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="37bed-282">Por exemplo, na exibição *Index.cshtml* , o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="37bed-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="37bed-283">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="37bed-284">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="37bed-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37bed-285">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="37bed-285">Additional resources</span></span>

* [<span data-ttu-id="37bed-286">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="37bed-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="37bed-287">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="37bed-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="37bed-288">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="37bed-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="37bed-289">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-291">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="37bed-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="37bed-292">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="37bed-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="37bed-294">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="37bed-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="37bed-296">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar**  >  **nova** classe  >  **vazia classe**.</span><span class="sxs-lookup"><span data-stu-id="37bed-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="37bed-297">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="37bed-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="37bed-298">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="37bed-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="37bed-299">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="37bed-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="37bed-300">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o tipo de dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="37bed-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="37bed-301">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="37bed-301">With this attribute:</span></span>

* <span data-ttu-id="37bed-302">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="37bed-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="37bed-303">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="37bed-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="37bed-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="37bed-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="37bed-305">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="37bed-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-307">No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="37bed-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="37bed-309">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="37bed-310">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="37bed-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="37bed-311">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="37bed-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="37bed-312">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="37bed-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-314">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="37bed-315">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="37bed-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="37bed-316">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="37bed-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="37bed-317">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="37bed-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="37bed-319">A caixa de diálogo **selecionar projetos** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="37bed-320">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="37bed-320">Press the **Ok** button.</span></span>

<span data-ttu-id="37bed-321">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="37bed-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="37bed-322">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="37bed-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="37bed-323">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="37bed-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="37bed-324">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-324">Create a database context class</span></span>

<span data-ttu-id="37bed-325">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="37bed-326">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="37bed-327">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="37bed-327">Create a *Data* folder.</span></span>

<span data-ttu-id="37bed-328">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="37bed-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="37bed-329">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="37bed-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="37bed-330">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="37bed-331">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="37bed-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="37bed-332">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-332">Register the database context</span></span>

<span data-ttu-id="37bed-333">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="37bed-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="37bed-334">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37bed-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="37bed-335">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="37bed-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="37bed-336">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="37bed-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="37bed-337">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="37bed-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="37bed-338">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="37bed-339">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="37bed-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-341">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="37bed-342">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="37bed-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="37bed-343">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="37bed-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="37bed-344">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-344">Add a database connection string</span></span>

<span data-ttu-id="37bed-345">Adicione uma cadeia de conexão ao *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="37bed-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-347">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="37bed-348">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="37bed-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="37bed-349">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="37bed-349">Scaffold movie pages</span></span>

<span data-ttu-id="37bed-350">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="37bed-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-352">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="37bed-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="37bed-354">Na caixa de diálogo **Adicionar Scaffold** , selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="37bed-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="37bed-356">Preencha a caixa de diálogo **Adicionar Controlador** :</span><span class="sxs-lookup"><span data-stu-id="37bed-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="37bed-357">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="37bed-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="37bed-358">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="37bed-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* <span data-ttu-id="37bed-360">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="37bed-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="37bed-361">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="37bed-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="37bed-362">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="37bed-362">Select **Add**</span></span>

<span data-ttu-id="37bed-363">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="37bed-363">Visual Studio creates:</span></span>

* <span data-ttu-id="37bed-364">Um controlador de filmes ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="37bed-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="37bed-365">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas ( *exibições/filmes/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="37bed-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="37bed-366">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="37bed-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="37bed-368">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs* , *Startup.cs* e *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="37bed-369">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="37bed-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="37bed-370">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-371">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="37bed-372">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs* , *Startup.cs* e *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="37bed-373">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="37bed-374">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="37bed-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="37bed-375">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="37bed-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="37bed-376">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="37bed-376">Initial migration</span></span>

<span data-ttu-id="37bed-377">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="37bed-378">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-380">No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="37bed-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="37bed-381">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="37bed-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="37bed-382">`Add-Migration InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="37bed-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="37bed-383">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="37bed-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="37bed-384">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="37bed-385">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="37bed-386">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="37bed-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="37bed-387">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="37bed-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="37bed-388">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="37bed-389">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="37bed-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="37bed-390">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="37bed-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="37bed-391">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="37bed-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="37bed-392">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="37bed-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="37bed-393">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="37bed-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-394">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="37bed-395">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="37bed-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="37bed-396">`ef migrations add InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="37bed-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="37bed-397">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="37bed-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="37bed-398">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="37bed-399">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="37bed-400">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="37bed-401">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="37bed-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="37bed-402">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="37bed-403">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="37bed-403">The InitialCreate class</span></span>

<span data-ttu-id="37bed-404">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="37bed-405">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="37bed-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="37bed-406">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="37bed-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="37bed-407">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="37bed-407">Test the app</span></span>

* <span data-ttu-id="37bed-408">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="37bed-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="37bed-409">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="37bed-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-411">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="37bed-412">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="37bed-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="37bed-413">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="37bed-413">Test the **Create** page.</span></span> <span data-ttu-id="37bed-414">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="37bed-415">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="37bed-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="37bed-416">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="37bed-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="37bed-417">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="37bed-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="37bed-418">Teste os links **Editar** , **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="37bed-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="37bed-419">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="37bed-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-421">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="37bed-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="37bed-422">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="37bed-423">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-424">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="37bed-425">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="37bed-426">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="37bed-427">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="37bed-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="37bed-428">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="37bed-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="37bed-429">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="37bed-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="37bed-430">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="37bed-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="37bed-431">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="37bed-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="37bed-432">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="37bed-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="37bed-433">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="37bed-434">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="37bed-435">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="37bed-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="37bed-436">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="37bed-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="37bed-437">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="37bed-438">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="37bed-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="37bed-439">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="37bed-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="37bed-440">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="37bed-441">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="37bed-442">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="37bed-443">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="37bed-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="37bed-444">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="37bed-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="37bed-445">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="37bed-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="37bed-446">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="37bed-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="37bed-447">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="37bed-448">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="37bed-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="37bed-449">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="37bed-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="37bed-450">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="37bed-451">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="37bed-452">Por exemplo, na exibição *Details.cshtml* , o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="37bed-453">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="37bed-454">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="37bed-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="37bed-455">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="37bed-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="37bed-456">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="37bed-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="37bed-457">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="37bed-458">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="37bed-459">Por exemplo, na exibição *Index.cshtml* , o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="37bed-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="37bed-460">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="37bed-461">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="37bed-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37bed-462">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="37bed-462">Additional resources</span></span>

* [<span data-ttu-id="37bed-463">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="37bed-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="37bed-464">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="37bed-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="37bed-465">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="37bed-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="37bed-466">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="37bed-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-468">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="37bed-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="37bed-469">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="37bed-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="37bed-471">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="37bed-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="37bed-472">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="37bed-472">Scaffold the movie model</span></span>

<span data-ttu-id="37bed-473">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="37bed-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="37bed-474">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="37bed-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-476">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="37bed-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="37bed-478">Na caixa de diálogo **Adicionar Scaffold** , selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="37bed-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="37bed-480">Preencha a caixa de diálogo **Adicionar Controlador** :</span><span class="sxs-lookup"><span data-stu-id="37bed-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="37bed-481">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="37bed-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="37bed-482">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="37bed-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="37bed-484">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="37bed-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="37bed-485">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="37bed-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="37bed-486">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="37bed-486">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="37bed-488">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="37bed-488">Visual Studio creates:</span></span>

* <span data-ttu-id="37bed-489">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core ( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="37bed-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="37bed-490">Um controlador de filmes ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="37bed-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="37bed-491">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas ( *exibições/filmes/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="37bed-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="37bed-492">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="37bed-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37bed-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37bed-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="37bed-494">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs* , *Startup.cs* e *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="37bed-495">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="37bed-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="37bed-496">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="37bed-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="37bed-497">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37bed-498">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="37bed-499">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs* , *Startup.cs* e *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="37bed-500">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="37bed-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="37bed-501">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="37bed-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="37bed-502">Se você executar o aplicativo e clicar no link **Filme do MVC** , receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="37bed-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-504">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="37bed-505">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="37bed-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="37bed-506">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="37bed-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="37bed-507">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="37bed-507">Initial migration</span></span>

<span data-ttu-id="37bed-508">Nesta seção, há estas tarefas:</span><span class="sxs-lookup"><span data-stu-id="37bed-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="37bed-509">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="37bed-509">Add an initial migration.</span></span>
* <span data-ttu-id="37bed-510">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="37bed-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="37bed-512">No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="37bed-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="37bed-514">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="37bed-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="37bed-515">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="37bed-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="37bed-516">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="37bed-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="37bed-517">O argumento `Initial` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="37bed-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="37bed-518">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="37bed-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="37bed-519">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="37bed-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="37bed-520">O `Update-Database` comando executa o `Up` método no arquivo *Migrations/{time-Stamp} _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-521">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="37bed-522">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="37bed-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="37bed-523">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="37bed-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="37bed-524">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="37bed-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="37bed-525">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="37bed-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="37bed-526">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="37bed-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="37bed-527">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="37bed-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="37bed-528">Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37bed-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="37bed-529">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="37bed-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="37bed-530">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="37bed-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37bed-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37bed-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37bed-532">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="37bed-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="37bed-533">Examine o seguinte método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="37bed-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="37bed-534">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="37bed-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="37bed-535">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="37bed-536">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="37bed-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="37bed-537">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="37bed-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="37bed-538">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="37bed-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="37bed-539">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="37bed-540">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="37bed-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="37bed-541">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="37bed-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="37bed-542">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="37bed-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="37bed-543">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="37bed-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="37bed-544">Você criou um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="37bed-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="37bed-545">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="37bed-545">Test the app</span></span>

* <span data-ttu-id="37bed-546">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="37bed-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="37bed-547">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="37bed-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="37bed-548">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="37bed-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="37bed-549">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="37bed-549">Test the **Create** link.</span></span> <span data-ttu-id="37bed-550">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="37bed-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="37bed-551">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="37bed-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="37bed-552">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="37bed-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="37bed-553">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="37bed-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="37bed-554">Teste os links **Editar** , **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="37bed-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="37bed-555">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="37bed-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="37bed-556">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="37bed-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="37bed-557">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="37bed-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="37bed-558">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="37bed-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="37bed-559">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="37bed-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="37bed-560">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="37bed-561">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="37bed-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="37bed-562">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="37bed-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="37bed-563">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="37bed-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="37bed-564">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="37bed-565">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="37bed-566">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="37bed-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="37bed-567">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="37bed-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="37bed-568">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="37bed-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="37bed-569">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="37bed-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="37bed-570">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="37bed-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="37bed-571">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="37bed-572">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="37bed-573">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="37bed-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="37bed-574">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="37bed-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="37bed-575">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="37bed-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="37bed-576">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="37bed-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="37bed-577">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="37bed-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="37bed-578">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="37bed-579">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="37bed-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="37bed-580">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="37bed-581">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="37bed-582">Por exemplo, na exibição *Details.cshtml* , o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="37bed-583">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="37bed-584">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="37bed-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="37bed-585">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="37bed-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="37bed-586">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="37bed-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="37bed-587">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="37bed-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="37bed-588">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="37bed-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="37bed-589">Por exemplo, na exibição *Index.cshtml* , o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="37bed-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="37bed-590">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="37bed-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="37bed-591">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="37bed-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37bed-592">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="37bed-592">Additional resources</span></span>

* [<span data-ttu-id="37bed-593">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="37bed-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="37bed-594">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="37bed-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="37bed-595">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com um banco de dados](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="37bed-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
