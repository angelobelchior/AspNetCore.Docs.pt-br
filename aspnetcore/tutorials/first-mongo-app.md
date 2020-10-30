---
title: Criar uma API Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web com o ASP.NET Core usando um banco de dados MongoDB NoSQL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060736"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="f7ecc-103">Criar uma API Web com o ASP.NET Core e o MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="f7ecc-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="f7ecc-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7ecc-105">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="f7ecc-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f7ecc-107">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-107">Configure MongoDB</span></span>
> * <span data-ttu-id="f7ecc-108">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="f7ecc-109">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="f7ecc-110">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="f7ecc-111">Personalizar a serialização JSON</span><span class="sxs-lookup"><span data-stu-id="f7ecc-111">Customize JSON serialization</span></span>

<span data-ttu-id="f7ecc-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7ecc-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f7ecc-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f7ecc-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7ecc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7ecc-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="f7ecc-115">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f7ecc-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="f7ecc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="f7ecc-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7ecc-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="f7ecc-119">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f7ecc-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="f7ecc-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="f7ecc-121">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="f7ecc-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7ecc-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f7ecc-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f7ecc-124">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f7ecc-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="f7ecc-125">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f7ecc-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="f7ecc-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="f7ecc-127">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-127">Configure MongoDB</span></span>

<span data-ttu-id="f7ecc-128">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="f7ecc-129">Adicione *C: \\ arquivos de \\ programas \\ do \\ \<version_number> \\ servidor MongoDB* à `Path` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="f7ecc-130">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="f7ecc-131">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="f7ecc-132">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="f7ecc-133">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="f7ecc-134">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="f7ecc-135">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="f7ecc-136">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="f7ecc-137">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-137">Open a command shell.</span></span> <span data-ttu-id="f7ecc-138">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="f7ecc-139">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="f7ecc-140">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-140">Open another command shell instance.</span></span> <span data-ttu-id="f7ecc-141">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="f7ecc-142">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="f7ecc-143">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="f7ecc-144">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="f7ecc-145">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="f7ecc-146">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="f7ecc-147">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="f7ecc-148">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="f7ecc-149">As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="f7ecc-150">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="f7ecc-151">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="f7ecc-152">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="f7ecc-153">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-153">The database is ready.</span></span> <span data-ttu-id="f7ecc-154">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="f7ecc-155">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7ecc-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7ecc-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7ecc-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7ecc-157">Vá para **arquivo** > **novo** > **projeto** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-157">Go to **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="f7ecc-158">Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-158">Select the **ASP.NET Core Web Application** project type, and select **Next** .</span></span>
1. <span data-ttu-id="f7ecc-159">Nomeie o projeto como *BooksApi* e selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-159">Name the project *BooksApi* , and select **Create** .</span></span>
1. <span data-ttu-id="f7ecc-160">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 3.0** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0** .</span></span> <span data-ttu-id="f7ecc-161">Selecione o modelo de projeto **API** e, em seguida, **Criar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-161">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="f7ecc-162">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="f7ecc-163">Na janela **Console do Gerenciador de Pacotes** , navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="f7ecc-164">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7ecc-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f7ecc-166">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="f7ecc-167">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="f7ecc-168">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' BooksApi '. Adicioná-los?** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?** .</span></span> <span data-ttu-id="f7ecc-169">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-169">Select **Yes** .</span></span>
1. <span data-ttu-id="f7ecc-170">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="f7ecc-171">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="f7ecc-172">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7ecc-173">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f7ecc-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7ecc-174">Em Visual Studio para Mac anteriores à versão 8,6, selecione **arquivo**  >  **nova solução**  >  **.NET Core**  >  **aplicativo** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="f7ecc-175">Na versão 8,6 ou posterior, selecione **arquivo**  >  **nova solução**  >  **Web e**  >  **aplicativo** de console na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="f7ecc-176">Selecione o modelo de projeto **ASP.NET Core** > **API** C# e selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next** .</span></span>
1. <span data-ttu-id="f7ecc-177">Selecione **.NET Core 3,1** na lista suspensa **estrutura de destino** e selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next** .</span></span>
1. <span data-ttu-id="f7ecc-178">Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-178">Enter *BooksApi* for the **Project Name** , and select **Create** .</span></span>
1. <span data-ttu-id="f7ecc-179">No painel **Solução** , clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages** .</span></span>
1. <span data-ttu-id="f7ecc-180">Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package** .</span></span>
1. <span data-ttu-id="f7ecc-181">Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="f7ecc-182">Adicionar um modelo de entidade</span><span class="sxs-lookup"><span data-stu-id="f7ecc-182">Add an entity model</span></span>

1. <span data-ttu-id="f7ecc-183">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="f7ecc-184">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="f7ecc-185">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="f7ecc-186">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="f7ecc-187">É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="f7ecc-188">É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="f7ecc-189">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="f7ecc-190">A `BookName` propriedade é anotada com o [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="f7ecc-191">O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="f7ecc-192">Adicionar um modelo de configuração</span><span class="sxs-lookup"><span data-stu-id="f7ecc-192">Add a configuration model</span></span>

1. <span data-ttu-id="f7ecc-193">Adicione os seguintes valores de configuração de banco de dados a *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="f7ecc-193">Add the following database configuration values to *:::no-loc(appsettings.json):::* :</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/:::no-loc(appsettings.json):::?highlight=2-6)]

1. <span data-ttu-id="f7ecc-194">Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="f7ecc-195">A `BookstoreDatabaseSettings` classe anterior é usada para armazenar os *:::no-loc(appsettings.json):::* valores de `BookstoreDatabaseSettings` Propriedade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-195">The preceding `BookstoreDatabaseSettings` class is used to store the *:::no-loc(appsettings.json):::* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="f7ecc-196">Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="f7ecc-197">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="f7ecc-198">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-198">In the preceding code:</span></span>

   * <span data-ttu-id="f7ecc-199">A instância de configuração à qual a *:::no-loc(appsettings.json):::* seção do arquivo `BookstoreDatabaseSettings` é associada é registrada no contêiner injeção de dependência (di).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-199">The configuration instance to which the *:::no-loc(appsettings.json):::* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="f7ecc-200">Por exemplo, a `BookstoreDatabaseSettings` propriedade de um objeto `ConnectionString` é populada com a `BookstoreDatabaseSettings:ConnectionString` propriedade em *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *:::no-loc(appsettings.json):::* .</span></span>
   * <span data-ttu-id="f7ecc-201">A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f7ecc-202">Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="f7ecc-203">Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="f7ecc-204">Adicionar um serviço de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="f7ecc-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="f7ecc-205">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="f7ecc-206">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="f7ecc-207">No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="f7ecc-208">Essa técnica fornece acesso aos *:::no-loc(appsettings.json):::* valores de configuração que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-208">This technique provides access to the *:::no-loc(appsettings.json):::* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="f7ecc-209">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="f7ecc-210">No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="f7ecc-211">O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="f7ecc-212">De acordo com as diretrizes oficiais de [reutilização do cliente mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` o deve ser registrado em di com um tempo de vida do serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="f7ecc-213">Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="f7ecc-214">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="f7ecc-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lê a instância de servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="f7ecc-216">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="f7ecc-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa o banco de dados Mongo para executar operações.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="f7ecc-218">Este tutorial usa o método [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="f7ecc-219">Execute operações CRUD em relação à coleção depois que esse método for chamado.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="f7ecc-220">Na chamada de método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="f7ecc-221">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="f7ecc-222">`TDocument` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="f7ecc-223">`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="f7ecc-224">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="f7ecc-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): exclui um único documento correspondente aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="f7ecc-226">[Localizar \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="f7ecc-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="f7ecc-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): substitui o documento único que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="f7ecc-229">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="f7ecc-229">Add a controller</span></span>

<span data-ttu-id="f7ecc-230">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="f7ecc-231">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-231">The preceding web API controller:</span></span>

* <span data-ttu-id="f7ecc-232">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="f7ecc-233">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="f7ecc-234">Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="f7ecc-235">O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="f7ecc-236">`CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="f7ecc-237">O cabeçalho `Location` especifica o URI do livro recém-criado.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="f7ecc-238">Testar a API Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-238">Test the web API</span></span>

1. <span data-ttu-id="f7ecc-239">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-239">Build and run the app.</span></span>

1. <span data-ttu-id="f7ecc-240">Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="f7ecc-241">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="f7ecc-242">Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="f7ecc-243">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="f7ecc-244">Configurar opções de serialização JSON</span><span class="sxs-lookup"><span data-stu-id="f7ecc-244">Configure JSON serialization options</span></span>

<span data-ttu-id="f7ecc-245">Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="f7ecc-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="f7ecc-246">O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="f7ecc-247">A propriedade `bookName` deve ser retornada como `Name`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="f7ecc-248">Para cumprir os requisitos anteriores, faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="f7ecc-249">O JSON.NET foi removido da estrutura compartilhada do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="f7ecc-250">Adicione uma referência de pacote a [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="f7ecc-251">Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="f7ecc-252">Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="f7ecc-253">Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="f7ecc-254">Em *Models/book. cs* , anote a `BookName` propriedade com o seguinte [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributo:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-254">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="f7ecc-255">O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="f7ecc-256">Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="f7ecc-257">Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="f7ecc-258">Observe a diferença em nomes de propriedade JSON.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7ecc-259">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="f7ecc-260">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f7ecc-261">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-261">Configure MongoDB</span></span>
> * <span data-ttu-id="f7ecc-262">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="f7ecc-263">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="f7ecc-264">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="f7ecc-265">Personalizar a serialização JSON</span><span class="sxs-lookup"><span data-stu-id="f7ecc-265">Customize JSON serialization</span></span>

<span data-ttu-id="f7ecc-266">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7ecc-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f7ecc-267">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f7ecc-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7ecc-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7ecc-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="f7ecc-269">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="f7ecc-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="f7ecc-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="f7ecc-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7ecc-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="f7ecc-273">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="f7ecc-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="f7ecc-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="f7ecc-275">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="f7ecc-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7ecc-277">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f7ecc-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f7ecc-278">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="f7ecc-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="f7ecc-279">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f7ecc-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="f7ecc-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="f7ecc-281">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="f7ecc-281">Configure MongoDB</span></span>

<span data-ttu-id="f7ecc-282">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="f7ecc-283">Adicione *C: \\ arquivos de \\ programas \\ do \\ \<version_number> \\ servidor MongoDB* à `Path` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="f7ecc-284">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="f7ecc-285">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="f7ecc-286">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="f7ecc-287">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="f7ecc-288">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="f7ecc-289">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="f7ecc-290">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="f7ecc-291">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-291">Open a command shell.</span></span> <span data-ttu-id="f7ecc-292">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="f7ecc-293">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="f7ecc-294">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-294">Open another command shell instance.</span></span> <span data-ttu-id="f7ecc-295">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="f7ecc-296">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="f7ecc-297">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="f7ecc-298">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="f7ecc-299">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="f7ecc-300">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="f7ecc-301">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="f7ecc-302">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="f7ecc-303">As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="f7ecc-304">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="f7ecc-305">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="f7ecc-306">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="f7ecc-307">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-307">The database is ready.</span></span> <span data-ttu-id="f7ecc-308">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="f7ecc-309">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7ecc-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7ecc-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7ecc-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7ecc-311">Vá para **arquivo** > **novo** > **projeto** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-311">Go to **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="f7ecc-312">Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-312">Select the **ASP.NET Core Web Application** project type, and select **Next** .</span></span>
1. <span data-ttu-id="f7ecc-313">Nomeie o projeto como *BooksApi* e selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-313">Name the project *BooksApi* , and select **Create** .</span></span>
1. <span data-ttu-id="f7ecc-314">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2** .</span></span> <span data-ttu-id="f7ecc-315">Selecione o modelo de projeto **API** e, em seguida, **Criar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-315">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="f7ecc-316">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="f7ecc-317">Na janela **Console do Gerenciador de Pacotes** , navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="f7ecc-318">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7ecc-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7ecc-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f7ecc-320">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="f7ecc-321">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="f7ecc-322">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' BooksApi '. Adicioná-los?** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?** .</span></span> <span data-ttu-id="f7ecc-323">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-323">Select **Yes** .</span></span>
1. <span data-ttu-id="f7ecc-324">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="f7ecc-325">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="f7ecc-326">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7ecc-327">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f7ecc-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7ecc-328">Em Visual Studio para Mac anteriores à versão 8,6, selecione **arquivo**  >  **nova solução**  >  **.NET Core**  >  **aplicativo** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="f7ecc-329">Na versão 8,6 ou posterior, selecione **arquivo**  >  **nova solução**  >  **Web e**  >  **aplicativo** de console na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="f7ecc-330">Selecione o modelo de projeto C# da **API Web do ASP.NET Core** e, em seguida, **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-330">Select the **ASP.NET Core Web API** C# project template, and select **Next** .</span></span>
1. <span data-ttu-id="f7ecc-331">Selecione **.NET Core 2.2** na lista suspensa **Estrutura de Destino** e, em seguida, **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next** .</span></span>
1. <span data-ttu-id="f7ecc-332">Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-332">Enter *BooksApi* for the **Project Name** , and select **Create** .</span></span>
1. <span data-ttu-id="f7ecc-333">No painel **Solução** , clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages** .</span></span>
1. <span data-ttu-id="f7ecc-334">Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package** .</span></span>
1. <span data-ttu-id="f7ecc-335">Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença** .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="f7ecc-336">Adicionar um modelo de entidade</span><span class="sxs-lookup"><span data-stu-id="f7ecc-336">Add an entity model</span></span>

1. <span data-ttu-id="f7ecc-337">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="f7ecc-338">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="f7ecc-339">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="f7ecc-340">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="f7ecc-341">É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="f7ecc-342">É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="f7ecc-343">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="f7ecc-344">A `BookName` propriedade é anotada com o [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="f7ecc-345">O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="f7ecc-346">Adicionar um modelo de configuração</span><span class="sxs-lookup"><span data-stu-id="f7ecc-346">Add a configuration model</span></span>

1. <span data-ttu-id="f7ecc-347">Adicione os seguintes valores de configuração de banco de dados a *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="f7ecc-347">Add the following database configuration values to *:::no-loc(appsettings.json):::* :</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/:::no-loc(appsettings.json):::?highlight=2-6)]

1. <span data-ttu-id="f7ecc-348">Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="f7ecc-349">A `BookstoreDatabaseSettings` classe anterior é usada para armazenar os *:::no-loc(appsettings.json):::* valores de `BookstoreDatabaseSettings` Propriedade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-349">The preceding `BookstoreDatabaseSettings` class is used to store the *:::no-loc(appsettings.json):::* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="f7ecc-350">Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="f7ecc-351">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="f7ecc-352">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-352">In the preceding code:</span></span>

   * <span data-ttu-id="f7ecc-353">A instância de configuração à qual a *:::no-loc(appsettings.json):::* seção do arquivo `BookstoreDatabaseSettings` é associada é registrada no contêiner injeção de dependência (di).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-353">The configuration instance to which the *:::no-loc(appsettings.json):::* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="f7ecc-354">Por exemplo, a `BookstoreDatabaseSettings` propriedade de um objeto `ConnectionString` é populada com a `BookstoreDatabaseSettings:ConnectionString` propriedade em *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *:::no-loc(appsettings.json):::* .</span></span>
   * <span data-ttu-id="f7ecc-355">A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f7ecc-356">Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="f7ecc-357">Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="f7ecc-358">Adicionar um serviço de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="f7ecc-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="f7ecc-359">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="f7ecc-360">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="f7ecc-361">No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="f7ecc-362">Essa técnica fornece acesso aos *:::no-loc(appsettings.json):::* valores de configuração que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="f7ecc-362">This technique provides access to the *:::no-loc(appsettings.json):::* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="f7ecc-363">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="f7ecc-364">No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="f7ecc-365">O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="f7ecc-366">De acordo com as diretrizes oficiais de [reutilização do cliente mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` o deve ser registrado em di com um tempo de vida do serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="f7ecc-367">Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="f7ecc-368">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="f7ecc-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lê a instância de servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="f7ecc-370">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="f7ecc-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa o banco de dados Mongo para executar operações.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="f7ecc-372">Este tutorial usa o método [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="f7ecc-373">Execute operações CRUD em relação à coleção depois que esse método for chamado.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="f7ecc-374">Na chamada de método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="f7ecc-375">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="f7ecc-376">`TDocument` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="f7ecc-377">`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="f7ecc-378">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="f7ecc-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): exclui um único documento correspondente aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="f7ecc-380">[Localizar \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="f7ecc-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="f7ecc-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): substitui o documento único que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="f7ecc-383">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="f7ecc-383">Add a controller</span></span>

<span data-ttu-id="f7ecc-384">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="f7ecc-385">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-385">The preceding web API controller:</span></span>

* <span data-ttu-id="f7ecc-386">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="f7ecc-387">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="f7ecc-388">Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="f7ecc-389">O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="f7ecc-390">`CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="f7ecc-391">O cabeçalho `Location` especifica o URI do livro recém-criado.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="f7ecc-392">Testar a API Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-392">Test the web API</span></span>

1. <span data-ttu-id="f7ecc-393">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-393">Build and run the app.</span></span>

1. <span data-ttu-id="f7ecc-394">Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="f7ecc-395">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="f7ecc-396">Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="f7ecc-397">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="f7ecc-398">Configurar opções de serialização JSON</span><span class="sxs-lookup"><span data-stu-id="f7ecc-398">Configure JSON serialization options</span></span>

<span data-ttu-id="f7ecc-399">Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="f7ecc-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="f7ecc-400">O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="f7ecc-401">A propriedade `bookName` deve ser retornada como `Name`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="f7ecc-402">Para cumprir os requisitos anteriores, faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="f7ecc-403">Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="f7ecc-404">Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="f7ecc-405">Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="f7ecc-406">Em *Models/book. cs* , anote a `BookName` propriedade com o seguinte [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributo:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-406">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="f7ecc-407">O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="f7ecc-408">Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="f7ecc-409">Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="f7ecc-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="f7ecc-410">Observe a diferença em nomes de propriedade JSON.</span><span class="sxs-lookup"><span data-stu-id="f7ecc-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="f7ecc-411">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="f7ecc-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="next-steps"></a><span data-ttu-id="f7ecc-412">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f7ecc-412">Next steps</span></span>

<span data-ttu-id="f7ecc-413">Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="f7ecc-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="f7ecc-414">Versão deste artigo no YouTube</span><span class="sxs-lookup"><span data-stu-id="f7ecc-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
