---
title: Parte 2, adicionar um modelo
author: rick-anderson
description: 'Parte 2 da série de tutoriais em :::no-loc(Razor)::: páginas. Nesta seção, as classes de modelo são adicionadas.'
ms.author: riande
ms.date: 09/30/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: ee1b7d77d8d209a11669ba29c8a951c59368180e
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360993"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="dc464-104">Parte 2, adicionar um modelo a um :::no-loc(Razor)::: aplicativo de páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dc464-104">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="dc464-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dc464-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="dc464-106">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="dc464-107">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="dc464-108">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="dc464-109">Primeiro, você escreve as classes de modelo e EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="dc464-110">As classes de modelo são conhecidas como classes POCO (de " **P** Lain- **o** LD **C** LR **o** bjects") porque não têm uma dependência em EF Core.</span><span class="sxs-lookup"><span data-stu-id="dc464-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="dc464-111">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="dc464-112">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dc464-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="dc464-113">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dc464-115">Em **Gerenciador de soluções** , clique com o botão direito do mouse no projeto *:::no-loc(Razor)::: PagesMovie* > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-115">In **Solution Explorer** , right-click the *:::no-loc(Razor):::PagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dc464-116">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="dc464-117">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="dc464-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="dc464-118">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="dc464-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="dc464-119">Dê à classe o nome *Movie*.</span><span class="sxs-lookup"><span data-stu-id="dc464-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="dc464-120">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-121">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-122">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-123">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-124">With this attribute:</span></span>

  * <span data-ttu-id="dc464-125">O usuário não é solicitado a inserir informações de hora no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dc464-128">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="dc464-129">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="dc464-130">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-131">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-132">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-133">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-134">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-134">With this attribute:</span></span>

  * <span data-ttu-id="dc464-135">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-136">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="dc464-137">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="dc464-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="dc464-138">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-138">Add a database context class</span></span>

1. <span data-ttu-id="dc464-139">No projeto *:::no-loc(Razor)::: PagesMovie* , crie uma pasta chamada *Data*.</span><span class="sxs-lookup"><span data-stu-id="dc464-139">In the *:::no-loc(Razor):::PagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="dc464-140">Na pasta *dados* , adicione um arquivo chamado *:::no-loc(Razor)::: PagesMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="dc464-140">In the *Data* folder, add a file named *:::no-loc(Razor):::PagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

   <span data-ttu-id="dc464-141">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="dc464-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="dc464-142">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="dc464-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="dc464-143">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="dc464-144">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-144">Add a database connection string</span></span>

<span data-ttu-id="dc464-145">Adicione uma cadeia de conexão ao *:::no-loc(appsettings.json):::* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="dc464-145">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="dc464-146">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-146">Register the database context</span></span>

1. <span data-ttu-id="dc464-147">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="dc464-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using :::no-loc(Razor):::PagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="dc464-148">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dc464-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-149">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dc464-150">Na **janela ferramenta de solução** , clique com o botão de controle no projeto *:::no-loc(Razor)::: PagesMovie* e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.</span><span class="sxs-lookup"><span data-stu-id="dc464-150">In the **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="dc464-151">Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="dc464-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="dc464-152">Na caixa de diálogo **Novo Arquivo** :</span><span class="sxs-lookup"><span data-stu-id="dc464-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="dc464-153">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="dc464-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="dc464-154">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="dc464-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="dc464-155">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="dc464-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="dc464-156">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-157">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-158">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-159">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-160">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-160">With this attribute:</span></span>

  * <span data-ttu-id="dc464-161">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-162">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="dc464-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="dc464-164">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="dc464-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dc464-165">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="dc464-165">Scaffold the movie model</span></span>

<span data-ttu-id="dc464-166">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="dc464-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dc464-167">Ou seja, a ferramenta scaffolding produz páginas para :::no-loc(Create)::: operações de, ler, atualizar e :::no-loc(Delete)::: (CRUD) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="dc464-167">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dc464-169">:::no-loc(Create)::: uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="dc464-169">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="dc464-170">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="dc464-171">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="dc464-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="dc464-172">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="dc464-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/5/sca.png)

1. <span data-ttu-id="dc464-174">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-174">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

1. <span data-ttu-id="dc464-176">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dc464-176">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="dc464-177">Na lista suspensa **classe de modelo** , selecione **filme ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dc464-177">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="dc464-178">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="dc464-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="dc464-179">Na caixa de diálogo **adicionar contexto de dados** , o nome da classe *:::no-loc(Razor)::: PagesMovie. Data. :::no-loc(Razor)::: PagesMovieContext* é gerado.</span><span class="sxs-lookup"><span data-stu-id="dc464-179">In the **Add Data Context** dialog, the class name *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="dc464-180">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-180">Select **Add**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="dc464-182">O *:::no-loc(appsettings.json):::* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="dc464-182">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="dc464-184">Abra um shell de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="dc464-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="dc464-185">**Para Windows** : execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="dc464-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dc464-186">**No macOS e Linux** , execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="dc464-187">A tabela a seguir detalha as opções do gerador de código ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc464-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="dc464-188">Opção</span><span class="sxs-lookup"><span data-stu-id="dc464-188">Option</span></span>               | <span data-ttu-id="dc464-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="dc464-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="dc464-190">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="dc464-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="dc464-191">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="dc464-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="dc464-192">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="dc464-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="dc464-193">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="dc464-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="dc464-194">Adiciona `_ValidationScriptsPartial` a edição e :::no-loc(Create)::: páginas</span><span class="sxs-lookup"><span data-stu-id="dc464-194">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="dc464-195">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="dc464-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="dc464-196">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="dc464-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="dc464-197">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="dc464-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="dc464-198">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dc464-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="dc464-199">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="dc464-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="dc464-200">`IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="dc464-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-201">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dc464-202">:::no-loc(Create)::: uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="dc464-202">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="dc464-203">Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="dc464-204">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="dc464-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="dc464-205">Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="dc464-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/scaMac.png)

1. <span data-ttu-id="dc464-207">Na caixa de diálogo **novo scaffolding** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-207">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="dc464-209">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dc464-209">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="dc464-210">Na **classe DbContext a ser usada:** Row, nomeie a classe *:::no-loc(Razor)::: PagesMovie. Data. :::no-loc(Razor)::: PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="dc464-210">In the **DbContext Class to use:** row, name the class *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext*.</span></span>
   1. <span data-ttu-id="dc464-211">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="dc464-211">Select **Finish**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/5/arpMac.png)

<span data-ttu-id="dc464-213">O *:::no-loc(appsettings.json):::* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="dc464-213">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="dc464-214">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="dc464-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="dc464-215">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dc464-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="dc464-216">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="dc464-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="dc464-217">`IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="dc464-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="dc464-218">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="dc464-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-220">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dc464-221">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-221">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="dc464-222">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-222">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dc464-223">Atualizado</span><span class="sxs-lookup"><span data-stu-id="dc464-223">Updated</span></span>

* <span data-ttu-id="dc464-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-224">*Startup.cs*</span></span>

<span data-ttu-id="dc464-225">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dc464-227">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="dc464-228">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-228">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="dc464-229">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-230">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dc464-231">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dc464-232">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-232">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="dc464-233">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-233">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dc464-234">Atualizado</span><span class="sxs-lookup"><span data-stu-id="dc464-234">Updated</span></span>

* <span data-ttu-id="dc464-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-235">*Startup.cs*</span></span>

<span data-ttu-id="dc464-236">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="dc464-237">:::no-loc(Create)::: o esquema de banco de dados inicial usando o recurso de migração do EF</span><span class="sxs-lookup"><span data-stu-id="dc464-237">:::no-loc(Create)::: the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="dc464-238">O recurso de migrações no Entity Framework Core fornece uma maneira de:</span><span class="sxs-lookup"><span data-stu-id="dc464-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="dc464-239">:::no-loc(Create)::: o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-239">:::no-loc(Create)::: the initial database schema.</span></span>
* <span data-ttu-id="dc464-240">Atualize incrementalmente o esquema de banco de dados para mantê-lo em sincronia com o modelo de dado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc464-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="dc464-241">O banco de dados existente é preservado.</span><span class="sxs-lookup"><span data-stu-id="dc464-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-243">Nesta seção, a janela do **Package Manager Console** (PMC) é usada para:</span><span class="sxs-lookup"><span data-stu-id="dc464-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="dc464-244">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-244">Add an initial migration.</span></span>
* <span data-ttu-id="dc464-245">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="dc464-246">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="dc464-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu do PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="dc464-248">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="dc464-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial:::no-loc(Create):::
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dc464-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="dc464-250">Execute os seguintes comandos da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="dc464-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add Initial:::no-loc(Create):::
  dotnet ef database update
  ```

---

<span data-ttu-id="dc464-251">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="dc464-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dc464-252">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="dc464-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dc464-253">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="dc464-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dc464-254">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="dc464-255">O comando `migrations` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dc464-256">O esquema é baseado no modelo especificado em `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="dc464-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="dc464-257">O argumento `Initial:::no-loc(Create):::` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="dc464-257">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="dc464-258">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="dc464-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dc464-259">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="dc464-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="dc464-260">Nesse caso, `update` o executa o `Up` método no arquivo *migrations/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dc464-262">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="dc464-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dc464-263">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dc464-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dc464-264">Serviços, como o contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc464-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dc464-265">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="dc464-265">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dc464-266">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="dc464-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dc464-267">A ferramenta scaffolding criou automaticamente um contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="dc464-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dc464-268">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc464-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dc464-269">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dc464-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="dc464-270">As `:::no-loc(Razor):::PagesMovieContext` coordenadas EF Core funcionalidade, como :::no-loc(Create)::: ler, atualizar e :::no-loc(Delete)::: , para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="dc464-270">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="dc464-271">O contexto de dados (`:::no-loc(Razor):::PagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="dc464-271">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="dc464-272">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="dc464-273">O código anterior cria uma [propriedade \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="dc464-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="dc464-274">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dc464-275">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="dc464-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dc464-276">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="dc464-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="dc464-277">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="dc464-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dc464-278">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dc464-279">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="dc464-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="dc464-280">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc464-280">Test the app</span></span>

1. <span data-ttu-id="dc464-281">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dc464-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="dc464-282">Se você receber o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="dc464-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="dc464-283">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dc464-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="dc464-284">Teste o **:::no-loc(Create):::** link.</span><span class="sxs-lookup"><span data-stu-id="dc464-284">Test the **:::no-loc(Create):::** link.</span></span>

   ![::: no-Loc (criar)::: página](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="dc464-286">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="dc464-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dc464-287">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="dc464-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dc464-288">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dc464-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="dc464-289">Teste a **edição** , os **detalhes** e os **:::no-loc(Delete):::** links.</span><span class="sxs-lookup"><span data-stu-id="dc464-289">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="dc464-290">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dc464-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc464-291">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc464-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dc464-292">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold :::no-loc(Razor)::: Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="dc464-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="dc464-293">Nesta seção, as classes são adicionadas para o gerenciamento de filmes.</span><span class="sxs-lookup"><span data-stu-id="dc464-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="dc464-294">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="dc464-295">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="dc464-296">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="dc464-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dc464-297">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="dc464-298">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dc464-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="dc464-299">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-301">Clique com o botão direito do mouse no projeto **:::no-loc(Razor)::: PagesMovie** > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-301">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dc464-302">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-302">Name the folder *Models*.</span></span>

<span data-ttu-id="dc464-303">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="dc464-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="dc464-304">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="dc464-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="dc464-305">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="dc464-305">Name the class **Movie**.</span></span>

<span data-ttu-id="dc464-306">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-307">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-308">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-309">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-310">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-310">With this attribute:</span></span>

  * <span data-ttu-id="dc464-311">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-312">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="dc464-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dc464-315">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dc464-316">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="dc464-317">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-318">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-319">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-320">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-321">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-321">With this attribute:</span></span>

  * <span data-ttu-id="dc464-322">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-323">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="dc464-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="dc464-325">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="dc464-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="dc464-326">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-326">Add a database context class</span></span>

* <span data-ttu-id="dc464-327">No projeto *:::no-loc(Razor)::: PagesMovie* , crie uma nova pasta denominada *dados*.</span><span class="sxs-lookup"><span data-stu-id="dc464-327">In the *:::no-loc(Razor):::PagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="dc464-328">Adicione a seguinte classe `:::no-loc(Razor):::PagesMovieContext` à pasta *Dados* :</span><span class="sxs-lookup"><span data-stu-id="dc464-328">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="dc464-329">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="dc464-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="dc464-330">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="dc464-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="dc464-331">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="dc464-332">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-332">Add a database connection string</span></span>

<span data-ttu-id="dc464-333">Adicione uma cadeia de conexão ao *:::no-loc(appsettings.json):::* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="dc464-333">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="dc464-334">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-334">Register the database context</span></span>

<span data-ttu-id="dc464-335">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="dc464-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="dc464-336">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc464-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-337">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dc464-338">Na **janela ferramenta de solução** , clique com o botão de controle no projeto **:::no-loc(Razor)::: PagesMovie** e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.</span><span class="sxs-lookup"><span data-stu-id="dc464-338">In the **Solution Tool Window** , control-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="dc464-339">Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="dc464-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="dc464-340">Na caixa de diálogo **Novo Arquivo** :</span><span class="sxs-lookup"><span data-stu-id="dc464-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dc464-341">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="dc464-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dc464-342">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="dc464-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dc464-343">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="dc464-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="dc464-344">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-345">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-346">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-347">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-348">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-348">With this attribute:</span></span>

  * <span data-ttu-id="dc464-349">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-350">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="dc464-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="dc464-352">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="dc464-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dc464-353">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="dc464-353">Scaffold the movie model</span></span>

<span data-ttu-id="dc464-354">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="dc464-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dc464-355">Ou seja, a ferramenta scaffolding produz páginas para :::no-loc(Create)::: operações de, ler, atualizar e :::no-loc(Delete)::: (CRUD) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="dc464-355">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-357">:::no-loc(Create)::: uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="dc464-357">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dc464-358">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dc464-359">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="dc464-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="dc464-360">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="dc464-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="dc464-362">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-362">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="dc464-364">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dc464-364">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dc464-365">Na lista suspensa **classe de modelo** , selecione **filme ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dc464-365">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="dc464-366">Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e altere o nome gerado de :::no-loc(Razor)::: PagesMovie. **Modelos**. :::no-loc(Razor)::: PagesMovieContext :::no-loc(Razor)::: PagesMovie. **Dados**. :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="dc464-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="dc464-367">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="dc464-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="dc464-368">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="dc464-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="dc464-369">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-369">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="dc464-371">O *:::no-loc(appsettings.json):::* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="dc464-371">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="dc464-373">Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="dc464-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="dc464-374">**Para Windows** : execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="dc464-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dc464-375">**No macOS e Linux** , execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="dc464-376">A tabela a seguir detalha as opções do gerador de código ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc464-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="dc464-377">Opção</span><span class="sxs-lookup"><span data-stu-id="dc464-377">Option</span></span>               | <span data-ttu-id="dc464-378">Descrição</span><span class="sxs-lookup"><span data-stu-id="dc464-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="dc464-379">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="dc464-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="dc464-380">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="dc464-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="dc464-381">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="dc464-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="dc464-382">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="dc464-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="dc464-383">Adiciona `_ValidationScriptsPartial` a edição e :::no-loc(Create)::: páginas</span><span class="sxs-lookup"><span data-stu-id="dc464-383">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="dc464-384">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="dc464-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="dc464-385">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="dc464-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="dc464-386">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="dc464-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="dc464-387">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dc464-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="dc464-388">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc464-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="dc464-389">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="dc464-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-390">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dc464-391">:::no-loc(Create)::: uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="dc464-391">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dc464-392">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dc464-393">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="dc464-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="dc464-394">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="dc464-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="dc464-396">Na caixa de diálogo **novo scaffolding** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-396">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="dc464-398">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dc464-398">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dc464-399">Na lista suspensa **classe de modelo** , selecione ou digite **filme ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dc464-399">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="dc464-400">Na linha **classe de contexto de dados** , digite o nome da nova classe, :::no-loc(Razor)::: PagesMovie. **Dados**. :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="dc464-400">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="dc464-401">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="dc464-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="dc464-402">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="dc464-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="dc464-403">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-403">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="dc464-405">O *:::no-loc(appsettings.json):::* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="dc464-405">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="dc464-406">Adicionar ferramentas do EF</span><span class="sxs-lookup"><span data-stu-id="dc464-406">Add EF tools</span></span>

<span data-ttu-id="dc464-407">Execute o seguinte comando de CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc464-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="dc464-408">O comando anterior adiciona as ferramentas de Entity Framework Core para o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc464-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="dc464-409">Para obter mais informações, consulte [referência de ferramentas de Entity Framework Core-CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="dc464-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="dc464-410">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="dc464-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="dc464-411">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dc464-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="dc464-412">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc464-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="dc464-413">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="dc464-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="dc464-414">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="dc464-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-416">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dc464-417">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-417">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="dc464-418">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-418">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dc464-419">Atualizado</span><span class="sxs-lookup"><span data-stu-id="dc464-419">Updated</span></span>

* <span data-ttu-id="dc464-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-420">*Startup.cs*</span></span>

<span data-ttu-id="dc464-421">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dc464-423">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dc464-424">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-424">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="dc464-425">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-425">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dc464-426">Atualizado</span><span class="sxs-lookup"><span data-stu-id="dc464-426">Updated</span></span>

* <span data-ttu-id="dc464-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-427">*Startup.cs*</span></span>

<span data-ttu-id="dc464-428">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dc464-430">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="dc464-431">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-431">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="dc464-432">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dc464-433">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="dc464-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-435">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="dc464-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dc464-436">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-436">Add an initial migration.</span></span>
* <span data-ttu-id="dc464-437">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="dc464-438">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="dc464-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dc464-440">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="dc464-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial:::no-loc(Create):::
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dc464-441">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="dc464-442">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc464-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="dc464-443">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="dc464-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dc464-444">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="dc464-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dc464-445">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="dc464-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dc464-446">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="dc464-447">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="dc464-448">O esquema é baseado no modelo especificado em `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="dc464-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="dc464-449">O argumento `Initial:::no-loc(Create):::` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="dc464-449">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="dc464-450">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="dc464-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dc464-451">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="dc464-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="dc464-452">Nesse caso, `update` o executa o `Up` método no arquivo  *migrations/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dc464-454">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="dc464-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dc464-455">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dc464-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dc464-456">Serviços, como o contexto de contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc464-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dc464-457">Os componentes que exigem esses serviços, como :::no-loc(Razor)::: páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="dc464-457">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="dc464-458">O código do construtor que obtém uma instância de contexto de contexto de banco de dados é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="dc464-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dc464-459">A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="dc464-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dc464-460">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc464-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dc464-461">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dc464-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="dc464-462">As `:::no-loc(Razor):::PagesMovieContext` coordenadas EF Core funcionalidade, como :::no-loc(Create)::: ler, atualizar e :::no-loc(Delete)::: , para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="dc464-462">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="dc464-463">O contexto de dados (`:::no-loc(Razor):::PagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="dc464-463">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="dc464-464">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="dc464-465">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="dc464-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dc464-466">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dc464-467">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="dc464-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dc464-468">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="dc464-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="dc464-469">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="dc464-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dc464-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dc464-471">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="dc464-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dc464-472">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc464-472">Test the app</span></span>

* <span data-ttu-id="dc464-473">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dc464-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dc464-474">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="dc464-474">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dc464-475">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dc464-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dc464-476">Teste o **:::no-loc(Create):::** link.</span><span class="sxs-lookup"><span data-stu-id="dc464-476">Test the **:::no-loc(Create):::** link.</span></span>

  ![::: no-Loc (criar)::: página](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="dc464-478">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="dc464-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dc464-479">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="dc464-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dc464-480">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dc464-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dc464-481">Teste a **edição** , os **detalhes** e os **:::no-loc(Delete):::** links.</span><span class="sxs-lookup"><span data-stu-id="dc464-481">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="dc464-482">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dc464-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc464-483">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc464-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dc464-484">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold :::no-loc(Razor)::: Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="dc464-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc464-485">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="dc464-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="dc464-486">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="dc464-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="dc464-487">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="dc464-488">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="dc464-489">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="dc464-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dc464-490">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="dc464-491">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dc464-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="dc464-492">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-494">Clique com o botão direito do mouse no projeto **:::no-loc(Razor)::: PagesMovie** > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-494">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dc464-495">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-495">Name the folder *Models*.</span></span>

<span data-ttu-id="dc464-496">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="dc464-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="dc464-497">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="dc464-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="dc464-498">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="dc464-498">Name the class **Movie**.</span></span>

<span data-ttu-id="dc464-499">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-500">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-501">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-502">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-503">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-503">With this attribute:</span></span>

  * <span data-ttu-id="dc464-504">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-505">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="dc464-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dc464-508">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dc464-509">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="dc464-510">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-511">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-512">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-513">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-514">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-514">With this attribute:</span></span>

  * <span data-ttu-id="dc464-515">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-516">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="dc464-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="dc464-518">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="dc464-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="dc464-519">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-519">Add a database context class</span></span>

<span data-ttu-id="dc464-520">No :::no-loc(Razor)::: projeto PagesMovie, crie uma nova pasta denominada *dados*.</span><span class="sxs-lookup"><span data-stu-id="dc464-520">In the :::no-loc(Razor):::PagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="dc464-521">Adicione a seguinte classe `:::no-loc(Razor):::PagesMovieContext` à pasta *Dados* :</span><span class="sxs-lookup"><span data-stu-id="dc464-521">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="dc464-522">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="dc464-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="dc464-523">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="dc464-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="dc464-524">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="dc464-525">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-525">Add a database connection string</span></span>

<span data-ttu-id="dc464-526">Adicione uma cadeia de conexão ao *:::no-loc(appsettings.json):::* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="dc464-526">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="dc464-527">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="dc464-527">Add required NuGet packages</span></span>

<span data-ttu-id="dc464-528">Execute o seguinte comando CLI do .NET Core para adicionar SQLite e CodeGeneration. Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="dc464-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="dc464-529">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dc464-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="dc464-530">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="dc464-530">Register the database context</span></span>

<span data-ttu-id="dc464-531">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="dc464-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="dc464-532">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc464-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="dc464-533">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="dc464-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-534">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dc464-535">Na **janela da ferramenta de solução** , clique com o botão de controle no projeto *:::no-loc(Razor)::: PagesMovie* e selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-535">In **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dc464-536">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="dc464-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="dc464-537">Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo**.</span><span class="sxs-lookup"><span data-stu-id="dc464-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="dc464-538">Na caixa de diálogo **Novo Arquivo** :</span><span class="sxs-lookup"><span data-stu-id="dc464-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dc464-539">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="dc464-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dc464-540">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="dc464-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dc464-541">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="dc464-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="dc464-542">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="dc464-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="dc464-543">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="dc464-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="dc464-544">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="dc464-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="dc464-545">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="dc464-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="dc464-546">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="dc464-546">With this attribute:</span></span>

  * <span data-ttu-id="dc464-547">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="dc464-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="dc464-548">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="dc464-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="dc464-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="dc464-550">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="dc464-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dc464-551">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="dc464-551">Scaffold the movie model</span></span>

<span data-ttu-id="dc464-552">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="dc464-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dc464-553">Ou seja, a ferramenta scaffolding produz páginas para :::no-loc(Create)::: operações de, ler, atualizar e :::no-loc(Delete)::: (CRUD) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="dc464-553">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-555">:::no-loc(Create)::: uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="dc464-555">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dc464-556">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dc464-557">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="dc464-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="dc464-558">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="dc464-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="dc464-560">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-560">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="dc464-562">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dc464-562">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="dc464-563">Na lista suspensa **classe de modelo** , selecione **filme ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dc464-563">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="dc464-564">Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e aceite o nome gerado **:::no-loc(Razor)::: PagesMovie. Models. :::no-loc(Razor)::: PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="dc464-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="dc464-565">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-565">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="dc464-567">O *:::no-loc(appsettings.json):::* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="dc464-567">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dc464-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dc464-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="dc464-569">Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="dc464-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="dc464-570">**Para Windows** : execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="dc464-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dc464-571">**No macOS e Linux** , execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="dc464-572">A tabela a seguir detalha as opções do gerador de código ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc464-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="dc464-573">Opção</span><span class="sxs-lookup"><span data-stu-id="dc464-573">Option</span></span>               | <span data-ttu-id="dc464-574">Descrição</span><span class="sxs-lookup"><span data-stu-id="dc464-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="dc464-575">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="dc464-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="dc464-576">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="dc464-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="dc464-577">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="dc464-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="dc464-578">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="dc464-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="dc464-579">Adiciona `_ValidationScriptsPartial` a edição e :::no-loc(Create)::: páginas</span><span class="sxs-lookup"><span data-stu-id="dc464-579">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="dc464-580">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="dc464-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="dc464-581">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="dc464-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc464-582">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dc464-583">:::no-loc(Create)::: uma pasta de *páginas/filmes* :</span><span class="sxs-lookup"><span data-stu-id="dc464-583">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dc464-584">Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="dc464-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dc464-585">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="dc464-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="dc464-586">Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="dc464-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="dc464-588">Na caixa de diálogo **Adicionar novo scaffolding** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-588">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="dc464-590">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dc464-590">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dc464-591">Na lista suspensa **classe de modelo** , selecione ou digite **filme**.</span><span class="sxs-lookup"><span data-stu-id="dc464-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="dc464-592">Na linha **classe de contexto de dados** , digite Select **:::no-loc(Razor)::: PagesMovieContext** isso criará uma nova classe de contexto de banco de dado com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="dc464-592">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="dc464-593">Nesse caso, será **:::no-loc(Razor)::: PagesMovie. Models. :::no-loc(Razor)::: PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="dc464-593">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="dc464-594">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc464-594">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="dc464-596">O *:::no-loc(appsettings.json):::* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="dc464-596">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="dc464-597">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc464-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="dc464-598">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="dc464-598">Files created</span></span>

* <span data-ttu-id="dc464-599">*Páginas/filmes* : :::no-loc(Create)::: , :::no-loc(Delete)::: , detalhes, editar e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="dc464-599">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="dc464-600">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-600">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="dc464-601">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="dc464-601">File updated</span></span>

* <span data-ttu-id="dc464-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dc464-602">*Startup.cs*</span></span>

<span data-ttu-id="dc464-603">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc464-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dc464-604">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="dc464-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dc464-606">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="dc464-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dc464-607">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-607">Add an initial migration.</span></span>
* <span data-ttu-id="dc464-608">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="dc464-609">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="dc464-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dc464-611">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="dc464-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="dc464-612">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="dc464-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dc464-613">O esquema é baseado no modelo especificado no `DbContext` , no arquivo *:::no-loc(Razor)::: PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="dc464-613">The schema is based on the model specified in the `DbContext`, in the *:::no-loc(Razor):::PagesMovieContext.cs* file.</span></span> <span data-ttu-id="dc464-614">O `Initial:::no-loc(Create):::` argumento é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="dc464-614">The `Initial:::no-loc(Create):::` argument is used to name the migration.</span></span> <span data-ttu-id="dc464-615">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="dc464-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="dc464-616">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="dc464-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="dc464-617">O `Update-Database` comando executa o `Up` método no arquivo *migrations/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* .</span><span class="sxs-lookup"><span data-stu-id="dc464-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file.</span></span> <span data-ttu-id="dc464-618">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dc464-619">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="dc464-620">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc464-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="dc464-621">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="dc464-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dc464-622">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="dc464-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dc464-623">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="dc464-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dc464-624">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="dc464-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc464-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc464-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dc464-626">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="dc464-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dc464-627">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dc464-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dc464-628">Serviços (como o contexto de contexto de banco de dados EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc464-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dc464-629">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="dc464-629">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dc464-630">O código do construtor que obtém uma instância de contexto contextB de contexto do banco de dados é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="dc464-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dc464-631">A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="dc464-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dc464-632">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc464-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dc464-633">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dc464-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="dc464-634">As `:::no-loc(Razor):::PagesMovieContext` coordenadas EF Core funcionalidade, como :::no-loc(Create)::: ler, atualizar e :::no-loc(Delete)::: , para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="dc464-634">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update, and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="dc464-635">O contexto de dados (`:::no-loc(Razor):::PagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="dc464-635">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="dc464-636">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="dc464-637">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="dc464-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dc464-638">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dc464-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dc464-639">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="dc464-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dc464-640">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="dc464-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="dc464-641">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="dc464-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dc464-642">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dc464-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dc464-643">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="dc464-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dc464-644">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc464-644">Test the app</span></span>

* <span data-ttu-id="dc464-645">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dc464-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dc464-646">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="dc464-646">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dc464-647">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dc464-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dc464-648">Teste o **:::no-loc(Create):::** link.</span><span class="sxs-lookup"><span data-stu-id="dc464-648">Test the **:::no-loc(Create):::** link.</span></span>

  ![::: no-Loc (criar)::: página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dc464-650">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="dc464-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dc464-651">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="dc464-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dc464-652">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dc464-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dc464-653">Teste a **edição** , os **detalhes** e os **:::no-loc(Delete):::** links.</span><span class="sxs-lookup"><span data-stu-id="dc464-653">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="dc464-654">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dc464-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc464-655">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc464-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dc464-656">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold :::no-loc(Razor)::: Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="dc464-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
