---
title: Parte 2, adicionar um modelo
author: rick-anderson
description: 'Parte 2 da série de tutoriais em Razor páginas. Nesta seção, as classes de modelo são adicionadas.'
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550656"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="0cc99-104">Parte 2, adicionar um modelo a um Razor aplicativo de páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc99-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="0cc99-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0cc99-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="0cc99-106">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="0cc99-107">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="0cc99-108">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="0cc99-109">Primeiro, você escreve as classes de modelo e EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="0cc99-110">As classes de modelo são conhecidas como classes POCO (de " **P** Lain- **o** LD **C** LR **o** bjects") porque não têm uma dependência em EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cc99-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="0cc99-111">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0cc99-112">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0cc99-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0cc99-113">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0cc99-115">Em **Gerenciador de soluções** , clique com o botão direito do mouse no projeto *Razor PagesMovie* > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-115">In **Solution Explorer** , right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0cc99-116">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="0cc99-117">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="0cc99-118">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="0cc99-119">Dê à classe o nome *Movie*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="0cc99-120">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-121">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-122">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-123">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-124">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-125">O usuário não é solicitado a inserir informações de hora no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0cc99-128">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="0cc99-129">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0cc99-130">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-131">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-132">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-133">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-134">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-134">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-135">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-136">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0cc99-137">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="0cc99-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0cc99-138">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-138">Add a database context class</span></span>

1. <span data-ttu-id="0cc99-139">No projeto *Razor PagesMovie* , crie uma pasta chamada *Data*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="0cc99-140">Na pasta *dados* , adicione um arquivo chamado *Razor PagesMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0cc99-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="0cc99-141">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cc99-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0cc99-142">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="0cc99-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0cc99-143">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0cc99-144">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-144">Add a database connection string</span></span>

<span data-ttu-id="0cc99-145">Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="0cc99-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0cc99-146">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-146">Register the database context</span></span>

1. <span data-ttu-id="0cc99-147">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="0cc99-148">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cc99-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-149">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0cc99-150">Na **janela ferramenta de solução** , clique com o botão de controle no projeto *Razor PagesMovie* e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.</span><span class="sxs-lookup"><span data-stu-id="0cc99-150">In the **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="0cc99-151">Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="0cc99-152">Na caixa de diálogo **Novo Arquivo** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="0cc99-153">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="0cc99-154">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="0cc99-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="0cc99-155">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="0cc99-156">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-157">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-158">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-159">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-160">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-160">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-161">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-162">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="0cc99-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="0cc99-164">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="0cc99-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0cc99-165">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="0cc99-165">Scaffold the movie model</span></span>

<span data-ttu-id="0cc99-166">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="0cc99-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0cc99-167">Ou seja, a ferramenta scaffolding produz páginas para Create operações de, ler, atualizar e Delete (CRUD) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="0cc99-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0cc99-169">Create uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="0cc99-170">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="0cc99-171">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="0cc99-172">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/5/sca.png)

1. <span data-ttu-id="0cc99-174">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

1. <span data-ttu-id="0cc99-176">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="0cc99-177">Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="0cc99-178">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="0cc99-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="0cc99-179">Na caixa de diálogo **adicionar contexto de dados** , o nome da classe *Razor PagesMovie. Data. Razor PagesMovieContext* é gerado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="0cc99-180">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-180">Select **Add**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="0cc99-182">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="0cc99-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0cc99-184">Abra um shell de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="0cc99-185">**Para Windows** : execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0cc99-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0cc99-186">**No macOS e Linux** , execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0cc99-187">A tabela a seguir detalha as opções do gerador de código ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0cc99-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="0cc99-188">Opção</span><span class="sxs-lookup"><span data-stu-id="0cc99-188">Option</span></span>               | <span data-ttu-id="0cc99-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="0cc99-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0cc99-190">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0cc99-191">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="0cc99-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0cc99-192">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc99-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0cc99-193">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="0cc99-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0cc99-194">Adiciona `_ValidationScriptsPartial` a edição e Create páginas</span><span class="sxs-lookup"><span data-stu-id="0cc99-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0cc99-195">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="0cc99-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0cc99-196">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0cc99-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0cc99-197">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="0cc99-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0cc99-198">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0cc99-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0cc99-199">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0cc99-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="0cc99-200">`IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-201">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0cc99-202">Create uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="0cc99-203">Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="0cc99-204">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="0cc99-205">Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/scaMac.png)

1. <span data-ttu-id="0cc99-207">Na caixa de diálogo **novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="0cc99-209">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="0cc99-210">Na **classe DbContext a ser usada:** Row, nomeie a classe *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="0cc99-211">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-211">Select **Finish**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/5/arpMac.png)

<span data-ttu-id="0cc99-213">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="0cc99-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0cc99-214">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="0cc99-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0cc99-215">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0cc99-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0cc99-216">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0cc99-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="0cc99-217">`IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="0cc99-218">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="0cc99-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-220">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0cc99-221">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-221">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0cc99-222">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0cc99-223">Atualizado</span><span class="sxs-lookup"><span data-stu-id="0cc99-223">Updated</span></span>

* <span data-ttu-id="0cc99-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-224">*Startup.cs*</span></span>

<span data-ttu-id="0cc99-225">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cc99-227">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="0cc99-228">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-228">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="0cc99-229">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-230">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0cc99-231">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0cc99-232">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-232">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0cc99-233">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0cc99-234">Atualizado</span><span class="sxs-lookup"><span data-stu-id="0cc99-234">Updated</span></span>

* <span data-ttu-id="0cc99-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-235">*Startup.cs*</span></span>

<span data-ttu-id="0cc99-236">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="0cc99-237">Create o esquema de banco de dados inicial usando o recurso de migração do EF</span><span class="sxs-lookup"><span data-stu-id="0cc99-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="0cc99-238">O recurso de migrações no Entity Framework Core fornece uma maneira de:</span><span class="sxs-lookup"><span data-stu-id="0cc99-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="0cc99-239">Create o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-239">Create the initial database schema.</span></span>
* <span data-ttu-id="0cc99-240">Atualize incrementalmente o esquema de banco de dados para mantê-lo em sincronia com o modelo de dado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="0cc99-241">O banco de dados existente é preservado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-243">Nesta seção, a janela do **Package Manager Console** (PMC) é usada para:</span><span class="sxs-lookup"><span data-stu-id="0cc99-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="0cc99-244">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-244">Add an initial migration.</span></span>
* <span data-ttu-id="0cc99-245">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="0cc99-246">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu do PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="0cc99-248">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0cc99-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="0cc99-250">Execute os seguintes comandos da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="0cc99-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="0cc99-251">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="0cc99-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0cc99-252">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc99-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0cc99-253">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="0cc99-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0cc99-254">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="0cc99-255">O comando `migrations` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0cc99-256">O esquema é baseado no modelo especificado em `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0cc99-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="0cc99-257">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="0cc99-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0cc99-258">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0cc99-259">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="0cc99-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="0cc99-260">Nesse caso, `update` o executa o `Up` método no arquivo *migrations/ \<time-stamp> _Initial Create . cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0cc99-262">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc99-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0cc99-263">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0cc99-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0cc99-264">Serviços, como o contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0cc99-265">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc99-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0cc99-266">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0cc99-267">A ferramenta scaffolding criou automaticamente um contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc99-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0cc99-268">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0cc99-269">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0cc99-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0cc99-270">As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como Create ler, atualizar e Delete , para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0cc99-271">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="0cc99-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0cc99-272">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0cc99-273">O código anterior cria uma [propriedade \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cc99-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="0cc99-274">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0cc99-275">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="0cc99-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0cc99-276">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="0cc99-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0cc99-277">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-278">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0cc99-279">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="0cc99-280">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0cc99-280">Test the app</span></span>

1. <span data-ttu-id="0cc99-281">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0cc99-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="0cc99-282">Se você receber o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="0cc99-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="0cc99-283">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0cc99-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="0cc99-284">Teste o **Create** link.</span><span class="sxs-lookup"><span data-stu-id="0cc99-284">Test the **Create** link.</span></span>

   ![::: no-Loc (criar)::: página](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="0cc99-286">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0cc99-287">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0cc99-288">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0cc99-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="0cc99-289">Teste a **edição** , os **detalhes** e os **Delete** links.</span><span class="sxs-lookup"><span data-stu-id="0cc99-289">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="0cc99-290">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0cc99-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cc99-291">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cc99-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0cc99-292">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="0cc99-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="0cc99-293">Nesta seção, as classes são adicionadas para o gerenciamento de filmes.</span><span class="sxs-lookup"><span data-stu-id="0cc99-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="0cc99-294">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="0cc99-295">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="0cc99-296">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cc99-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0cc99-297">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0cc99-298">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0cc99-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0cc99-299">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-301">Clique com o botão direito do mouse no projeto **Razor PagesMovie** > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0cc99-302">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-302">Name the folder *Models*.</span></span>

<span data-ttu-id="0cc99-303">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="0cc99-304">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="0cc99-305">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-305">Name the class **Movie**.</span></span>

<span data-ttu-id="0cc99-306">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-307">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-308">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-309">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-310">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-310">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-311">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-312">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0cc99-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cc99-315">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0cc99-316">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0cc99-317">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-318">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-319">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-320">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-321">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-321">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-322">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-323">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0cc99-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0cc99-325">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="0cc99-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0cc99-326">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-326">Add a database context class</span></span>

* <span data-ttu-id="0cc99-327">No projeto *Razor PagesMovie* , crie uma nova pasta denominada *dados*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="0cc99-328">Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0cc99-329">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cc99-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0cc99-330">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="0cc99-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0cc99-331">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0cc99-332">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-332">Add a database connection string</span></span>

<span data-ttu-id="0cc99-333">Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="0cc99-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0cc99-334">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-334">Register the database context</span></span>

<span data-ttu-id="0cc99-335">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0cc99-336">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-337">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0cc99-338">Na **janela ferramenta de solução** , clique com o botão de controle no projeto **Razor PagesMovie** e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.</span><span class="sxs-lookup"><span data-stu-id="0cc99-338">In the **Solution Tool Window** , control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="0cc99-339">Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="0cc99-340">Na caixa de diálogo **Novo Arquivo** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0cc99-341">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0cc99-342">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="0cc99-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0cc99-343">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="0cc99-344">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-345">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-346">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-347">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-348">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-348">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-349">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-350">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="0cc99-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="0cc99-352">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="0cc99-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0cc99-353">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="0cc99-353">Scaffold the movie model</span></span>

<span data-ttu-id="0cc99-354">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="0cc99-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0cc99-355">Ou seja, a ferramenta scaffolding produz páginas para Create operações de, ler, atualizar e Delete (CRUD) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="0cc99-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-357">Create uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0cc99-358">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0cc99-359">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="0cc99-360">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="0cc99-362">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="0cc99-364">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0cc99-365">Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0cc99-366">Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e altere o nome gerado de Razor PagesMovie. **Modelos**. Razor PagesMovieContext Razor PagesMovie. **Dados**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="0cc99-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="0cc99-367">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="0cc99-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="0cc99-368">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="0cc99-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="0cc99-369">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-369">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="0cc99-371">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="0cc99-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0cc99-373">Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="0cc99-374">**Para Windows** : execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0cc99-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0cc99-375">**No macOS e Linux** , execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0cc99-376">A tabela a seguir detalha as opções do gerador de código ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc99-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="0cc99-377">Opção</span><span class="sxs-lookup"><span data-stu-id="0cc99-377">Option</span></span>               | <span data-ttu-id="0cc99-378">Descrição</span><span class="sxs-lookup"><span data-stu-id="0cc99-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0cc99-379">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0cc99-380">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="0cc99-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0cc99-381">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc99-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0cc99-382">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="0cc99-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0cc99-383">Adiciona `_ValidationScriptsPartial` a edição e Create páginas</span><span class="sxs-lookup"><span data-stu-id="0cc99-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0cc99-384">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="0cc99-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0cc99-385">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0cc99-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0cc99-386">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="0cc99-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0cc99-387">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0cc99-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0cc99-388">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0cc99-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0cc99-389">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-390">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0cc99-391">Create uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0cc99-392">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0cc99-393">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="0cc99-394">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="0cc99-396">Na caixa de diálogo **novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="0cc99-398">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0cc99-399">Na lista suspensa **classe de modelo** , selecione ou digite **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0cc99-400">Na linha **classe de contexto de dados** , digite o nome da nova classe, Razor PagesMovie. **Dados**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="0cc99-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="0cc99-401">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="0cc99-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="0cc99-402">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="0cc99-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="0cc99-403">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-403">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="0cc99-405">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="0cc99-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="0cc99-406">Adicionar ferramentas do EF</span><span class="sxs-lookup"><span data-stu-id="0cc99-406">Add EF tools</span></span>

<span data-ttu-id="0cc99-407">Execute o seguinte comando de CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc99-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="0cc99-408">O comando anterior adiciona as ferramentas de Entity Framework Core para o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0cc99-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="0cc99-409">Para obter mais informações, consulte [referência de ferramentas de Entity Framework Core-CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="0cc99-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0cc99-410">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="0cc99-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0cc99-411">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0cc99-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0cc99-412">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0cc99-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0cc99-413">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="0cc99-414">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="0cc99-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-416">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0cc99-417">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-417">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0cc99-418">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0cc99-419">Atualizado</span><span class="sxs-lookup"><span data-stu-id="0cc99-419">Updated</span></span>

* <span data-ttu-id="0cc99-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-420">*Startup.cs*</span></span>

<span data-ttu-id="0cc99-421">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0cc99-423">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0cc99-424">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-424">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0cc99-425">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0cc99-426">Atualizado</span><span class="sxs-lookup"><span data-stu-id="0cc99-426">Updated</span></span>

* <span data-ttu-id="0cc99-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-427">*Startup.cs*</span></span>

<span data-ttu-id="0cc99-428">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0cc99-430">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="0cc99-431">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-431">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="0cc99-432">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0cc99-433">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="0cc99-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-435">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="0cc99-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0cc99-436">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-436">Add an initial migration.</span></span>
* <span data-ttu-id="0cc99-437">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="0cc99-438">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0cc99-440">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0cc99-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-441">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="0cc99-442">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc99-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="0cc99-443">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="0cc99-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0cc99-444">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc99-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0cc99-445">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="0cc99-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0cc99-446">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="0cc99-447">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="0cc99-448">O esquema é baseado no modelo especificado em `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0cc99-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="0cc99-449">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="0cc99-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0cc99-450">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0cc99-451">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="0cc99-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="0cc99-452">Nesse caso, `update` o executa o `Up` método no arquivo  *migrations/ \<time-stamp> _Initial Create . cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0cc99-454">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc99-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0cc99-455">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0cc99-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0cc99-456">Serviços, como o contexto de contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0cc99-457">Os componentes que exigem esses serviços, como Razor páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc99-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0cc99-458">O código do construtor que obtém uma instância de contexto de contexto de banco de dados é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0cc99-459">A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc99-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0cc99-460">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0cc99-461">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0cc99-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0cc99-462">As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como Create ler, atualizar e Delete , para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0cc99-463">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="0cc99-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0cc99-464">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0cc99-465">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cc99-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0cc99-466">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0cc99-467">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="0cc99-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0cc99-468">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="0cc99-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0cc99-469">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0cc99-471">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0cc99-472">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0cc99-472">Test the app</span></span>

* <span data-ttu-id="0cc99-473">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0cc99-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0cc99-474">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="0cc99-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0cc99-475">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0cc99-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0cc99-476">Teste o **Create** link.</span><span class="sxs-lookup"><span data-stu-id="0cc99-476">Test the **Create** link.</span></span>

  ![::: no-Loc (criar)::: página](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="0cc99-478">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0cc99-479">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0cc99-480">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0cc99-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0cc99-481">Teste a **edição** , os **detalhes** e os **Delete** links.</span><span class="sxs-lookup"><span data-stu-id="0cc99-481">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="0cc99-482">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0cc99-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cc99-483">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cc99-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0cc99-484">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="0cc99-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0cc99-485">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="0cc99-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="0cc99-486">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="0cc99-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="0cc99-487">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="0cc99-488">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="0cc99-489">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="0cc99-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0cc99-490">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0cc99-491">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0cc99-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0cc99-492">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-494">Clique com o botão direito do mouse no projeto **Razor PagesMovie** > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0cc99-495">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-495">Name the folder *Models*.</span></span>

<span data-ttu-id="0cc99-496">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="0cc99-497">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="0cc99-498">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-498">Name the class **Movie**.</span></span>

<span data-ttu-id="0cc99-499">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-500">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-501">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-502">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-503">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-503">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-504">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-505">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0cc99-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0cc99-508">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0cc99-509">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0cc99-510">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-511">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-512">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-513">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-514">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-514">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-515">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-516">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0cc99-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0cc99-518">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="0cc99-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0cc99-519">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-519">Add a database context class</span></span>

<span data-ttu-id="0cc99-520">No Razor projeto PagesMovie, crie uma nova pasta denominada *dados*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="0cc99-521">Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0cc99-522">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cc99-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0cc99-523">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="0cc99-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0cc99-524">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0cc99-525">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-525">Add a database connection string</span></span>

<span data-ttu-id="0cc99-526">Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="0cc99-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="0cc99-527">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="0cc99-527">Add required NuGet packages</span></span>

<span data-ttu-id="0cc99-528">Execute o seguinte comando CLI do .NET Core para adicionar SQLite e CodeGeneration. Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="0cc99-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="0cc99-529">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0cc99-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0cc99-530">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cc99-530">Register the database context</span></span>

<span data-ttu-id="0cc99-531">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0cc99-532">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="0cc99-533">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="0cc99-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-534">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0cc99-535">Na **janela da ferramenta de solução** , clique com o botão de controle no projeto *Razor PagesMovie* e selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-535">In **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="0cc99-536">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="0cc99-537">Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="0cc99-538">Na caixa de diálogo **Novo Arquivo** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0cc99-539">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0cc99-540">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="0cc99-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0cc99-541">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="0cc99-542">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0cc99-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0cc99-543">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="0cc99-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="0cc99-544">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0cc99-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0cc99-545">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0cc99-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0cc99-546">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="0cc99-546">With this attribute:</span></span>

  * <span data-ttu-id="0cc99-547">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="0cc99-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0cc99-548">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0cc99-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="0cc99-550">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="0cc99-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0cc99-551">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="0cc99-551">Scaffold the movie model</span></span>

<span data-ttu-id="0cc99-552">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="0cc99-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0cc99-553">Ou seja, a ferramenta scaffolding produz páginas para Create operações de, ler, atualizar e Delete (CRUD) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="0cc99-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-555">Create uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0cc99-556">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0cc99-557">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="0cc99-558">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="0cc99-560">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="0cc99-562">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="0cc99-563">Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0cc99-564">Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e aceite o nome gerado **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0cc99-565">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-565">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="0cc99-567">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="0cc99-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0cc99-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0cc99-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0cc99-569">Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="0cc99-570">**Para Windows** : execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0cc99-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0cc99-571">**No macOS e Linux** , execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0cc99-572">A tabela a seguir detalha as opções do gerador de código ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc99-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="0cc99-573">Opção</span><span class="sxs-lookup"><span data-stu-id="0cc99-573">Option</span></span>               | <span data-ttu-id="0cc99-574">Descrição</span><span class="sxs-lookup"><span data-stu-id="0cc99-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0cc99-575">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0cc99-576">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="0cc99-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0cc99-577">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc99-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0cc99-578">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="0cc99-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0cc99-579">Adiciona `_ValidationScriptsPartial` a edição e Create páginas</span><span class="sxs-lookup"><span data-stu-id="0cc99-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0cc99-580">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="0cc99-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0cc99-581">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0cc99-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-582">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0cc99-583">Create uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="0cc99-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0cc99-584">Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0cc99-585">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="0cc99-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="0cc99-586">Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="0cc99-588">Na caixa de diálogo **Adicionar novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="0cc99-590">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0cc99-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0cc99-591">Na lista suspensa **classe de modelo** , selecione ou digite **filme**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="0cc99-592">Na linha **classe de contexto de dados** , digite Select **Razor PagesMovieContext** isso criará uma nova classe de contexto de banco de dado com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="0cc99-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="0cc99-593">Nesse caso, será **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0cc99-594">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-594">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="0cc99-596">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="0cc99-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="0cc99-597">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc99-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0cc99-598">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="0cc99-598">Files created</span></span>

* <span data-ttu-id="0cc99-599">*Páginas/filmes* : Create , Delete , detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="0cc99-599">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0cc99-600">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="0cc99-601">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="0cc99-601">File updated</span></span>

* <span data-ttu-id="0cc99-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0cc99-602">*Startup.cs*</span></span>

<span data-ttu-id="0cc99-603">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0cc99-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0cc99-604">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="0cc99-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cc99-606">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="0cc99-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0cc99-607">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-607">Add an initial migration.</span></span>
* <span data-ttu-id="0cc99-608">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="0cc99-609">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="0cc99-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0cc99-611">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0cc99-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="0cc99-612">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0cc99-613">O esquema é baseado no modelo especificado no `DbContext` , no arquivo *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="0cc99-614">O `InitialCreate` argumento é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="0cc99-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="0cc99-615">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="0cc99-616">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="0cc99-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="0cc99-617">O `Update-Database` comando executa o `Up` método no arquivo *migrations/ \<time-stamp> _Initial Create . cs* .</span><span class="sxs-lookup"><span data-stu-id="0cc99-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="0cc99-618">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-619">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="0cc99-620">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc99-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="0cc99-621">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="0cc99-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0cc99-622">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc99-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0cc99-623">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="0cc99-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0cc99-624">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="0cc99-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cc99-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cc99-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0cc99-626">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc99-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0cc99-627">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0cc99-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0cc99-628">Serviços (como o contexto de contexto de banco de dados EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0cc99-629">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc99-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0cc99-630">O código do construtor que obtém uma instância de contexto contextB de contexto do banco de dados é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0cc99-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0cc99-631">A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc99-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0cc99-632">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0cc99-633">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0cc99-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="0cc99-634">As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como Create ler, atualizar e Delete , para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0cc99-635">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="0cc99-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0cc99-636">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0cc99-637">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cc99-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0cc99-638">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc99-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0cc99-639">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="0cc99-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0cc99-640">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="0cc99-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0cc99-641">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0cc99-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0cc99-642">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0cc99-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0cc99-643">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0cc99-644">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0cc99-644">Test the app</span></span>

* <span data-ttu-id="0cc99-645">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0cc99-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0cc99-646">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="0cc99-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0cc99-647">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0cc99-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0cc99-648">Teste o **Create** link.</span><span class="sxs-lookup"><span data-stu-id="0cc99-648">Test the **Create** link.</span></span>

  ![::: no-Loc (criar)::: página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="0cc99-650">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="0cc99-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0cc99-651">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="0cc99-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0cc99-652">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0cc99-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0cc99-653">Teste a **edição** , os **detalhes** e os **Delete** links.</span><span class="sxs-lookup"><span data-stu-id="0cc99-653">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="0cc99-654">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0cc99-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cc99-655">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cc99-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0cc99-656">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="0cc99-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
