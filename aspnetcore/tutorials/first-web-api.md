---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
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
uid: tutorials/first-web-api
ms.openlocfilehash: ce0dfdf1ce88b55790d33918a2d20bc19a09b288
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626890"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="6eadb-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eadb-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="6eadb-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="6eadb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="6eadb-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6eadb-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6eadb-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="6eadb-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6eadb-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-107">Create a web API project.</span></span>
> * <span data-ttu-id="6eadb-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="6eadb-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="6eadb-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="6eadb-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="6eadb-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-111">Call the web API with Postman.</span></span>

<span data-ttu-id="6eadb-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="6eadb-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="6eadb-113">Overview</span></span>

<span data-ttu-id="6eadb-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="6eadb-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="6eadb-115">API</span><span class="sxs-lookup"><span data-stu-id="6eadb-115">API</span></span> | <span data-ttu-id="6eadb-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="6eadb-116">Description</span></span> | <span data-ttu-id="6eadb-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="6eadb-117">Request body</span></span> | <span data-ttu-id="6eadb-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="6eadb-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="6eadb-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-119">Get all to-do items</span></span> | <span data-ttu-id="6eadb-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-120">None</span></span> | <span data-ttu-id="6eadb-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="6eadb-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="6eadb-122">Get an item by ID</span></span> | <span data-ttu-id="6eadb-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-123">None</span></span> | <span data-ttu-id="6eadb-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="6eadb-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="6eadb-125">Add a new item</span></span> | <span data-ttu-id="6eadb-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-126">To-do item</span></span> | <span data-ttu-id="6eadb-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="6eadb-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="6eadb-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="6eadb-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-129">To-do item</span></span> | <span data-ttu-id="6eadb-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-130">None</span></span> |
|<span data-ttu-id="6eadb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6eadb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="6eadb-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="6eadb-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="6eadb-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-133">None</span></span> | <span data-ttu-id="6eadb-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-134">None</span></span>|

<span data-ttu-id="6eadb-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="6eadb-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6eadb-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="6eadb-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="6eadb-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-147">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6eadb-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="6eadb-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="6eadb-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="6eadb-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6eadb-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6eadb-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6eadb-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="6eadb-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6eadb-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="6eadb-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="6eadb-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="6eadb-158">The preceding commands:</span></span>

  * <span data-ttu-id="6eadb-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6eadb-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="6eadb-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="6eadb-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6eadb-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="6eadb-164">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="6eadb-165">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="6eadb-167">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="6eadb-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-168">Select **Next**.</span></span>

* <span data-ttu-id="6eadb-169">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="6eadb-171">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6eadb-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="6eadb-172">Testar a API</span><span class="sxs-lookup"><span data-stu-id="6eadb-172">Test the API</span></span>

<span data-ttu-id="6eadb-173">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="6eadb-174">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6eadb-176">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6eadb-177">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="6eadb-178">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="6eadb-179">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6eadb-181">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6eadb-182">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-183">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6eadb-184">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="6eadb-185">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="6eadb-186">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="6eadb-187">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="6eadb-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="6eadb-188">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="6eadb-188">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="6eadb-189">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="6eadb-189">Add a model class</span></span>

<span data-ttu-id="6eadb-190">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="6eadb-191">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-193">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="6eadb-194">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6eadb-195">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="6eadb-196">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6eadb-197">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="6eadb-198">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6eadb-200">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="6eadb-201">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6eadb-203">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-203">Right-click the project.</span></span> <span data-ttu-id="6eadb-204">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6eadb-205">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-205">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="6eadb-207">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="6eadb-208">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="6eadb-209">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="6eadb-210">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="6eadb-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="6eadb-211">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="6eadb-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="6eadb-212">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6eadb-212">Add a database context</span></span>

<span data-ttu-id="6eadb-213">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="6eadb-214">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="6eadb-216">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="6eadb-216">Add NuGet packages</span></span>

* <span data-ttu-id="6eadb-217">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="6eadb-218">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="6eadb-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="6eadb-219">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="6eadb-220">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="6eadb-221">Use as instruções anteriores para adicionar o pacote NuGet **Microsoft. EntityFrameworkCore. inmemory** .</span><span class="sxs-lookup"><span data-stu-id="6eadb-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="6eadb-223">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="6eadb-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="6eadb-224">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6eadb-225">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-226">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6eadb-227">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="6eadb-228">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="6eadb-229">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6eadb-229">Register the database context</span></span>

<span data-ttu-id="6eadb-230">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6eadb-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6eadb-231">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="6eadb-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="6eadb-232">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="6eadb-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="6eadb-233">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6eadb-233">The preceding code:</span></span>

* <span data-ttu-id="6eadb-234">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="6eadb-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="6eadb-235">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6eadb-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="6eadb-236">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="6eadb-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="6eadb-237">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="6eadb-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-239">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="6eadb-240">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6eadb-241">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="6eadb-242">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="6eadb-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="6eadb-243">Selecione **TodoItem (TodoApi. Models)** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="6eadb-244">Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="6eadb-245">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-246">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6eadb-247">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6eadb-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet-aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="6eadb-248">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="6eadb-248">The preceding commands:</span></span>

* <span data-ttu-id="6eadb-249">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6eadb-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="6eadb-250">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="6eadb-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="6eadb-251">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="6eadb-252">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="6eadb-252">The generated code:</span></span>

* <span data-ttu-id="6eadb-253">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="6eadb-254">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="6eadb-255">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="6eadb-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="6eadb-256">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="6eadb-257">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="6eadb-258">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="6eadb-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="6eadb-259">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="6eadb-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="6eadb-260">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="6eadb-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="6eadb-261">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="6eadb-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="6eadb-262">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="6eadb-263">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="6eadb-264">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="6eadb-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="6eadb-265">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="6eadb-266">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6eadb-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="6eadb-267">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6eadb-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6eadb-268">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="6eadb-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="6eadb-269">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="6eadb-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="6eadb-270">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="6eadb-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="6eadb-271">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="6eadb-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="6eadb-272">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="6eadb-273">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6eadb-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="6eadb-274">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="6eadb-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="6eadb-275">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="6eadb-276">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="6eadb-276">Install Postman</span></span>

<span data-ttu-id="6eadb-277">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="6eadb-278">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="6eadb-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="6eadb-279">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-279">Start the web app.</span></span>
* <span data-ttu-id="6eadb-280">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-280">Start Postman.</span></span>
* <span data-ttu-id="6eadb-281">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="6eadb-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="6eadb-282">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="6eadb-283">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="6eadb-284">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="6eadb-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="6eadb-285">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6eadb-285">Create a new request.</span></span>
* <span data-ttu-id="6eadb-286">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="6eadb-287">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="6eadb-288">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="6eadb-289">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="6eadb-290">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="6eadb-291">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="6eadb-292">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6eadb-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="6eadb-293">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-293">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="6eadb-295">Testar o URI do cabeçalho do local com o postmaster</span><span class="sxs-lookup"><span data-stu-id="6eadb-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="6eadb-296">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="6eadb-297">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="6eadb-297">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="6eadb-299">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="6eadb-300">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="6eadb-301">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="6eadb-302">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="6eadb-303">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="6eadb-303">Examine the GET methods</span></span>

<span data-ttu-id="6eadb-304">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="6eadb-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="6eadb-305">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="6eadb-306">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6eadb-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="6eadb-307">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="6eadb-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="6eadb-308">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="6eadb-308">Test Get with Postman</span></span>

* <span data-ttu-id="6eadb-309">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6eadb-309">Create a new request.</span></span>
* <span data-ttu-id="6eadb-310">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="6eadb-311">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="6eadb-312">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="6eadb-313">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="6eadb-314">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-314">Select **Send**.</span></span>

<span data-ttu-id="6eadb-315">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="6eadb-315">This app uses an in-memory database.</span></span> <span data-ttu-id="6eadb-316">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="6eadb-317">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="6eadb-318">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="6eadb-318">Routing and URL paths</span></span>

<span data-ttu-id="6eadb-319">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="6eadb-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="6eadb-320">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6eadb-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="6eadb-321">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="6eadb-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="6eadb-322">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="6eadb-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="6eadb-323">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="6eadb-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="6eadb-324">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6eadb-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="6eadb-325">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="6eadb-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="6eadb-326">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-326">This sample doesn't use a template.</span></span> <span data-ttu-id="6eadb-327">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6eadb-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6eadb-328">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="6eadb-329">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6eadb-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="6eadb-330">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="6eadb-330">Return values</span></span>

<span data-ttu-id="6eadb-331">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="6eadb-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="6eadb-332">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="6eadb-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="6eadb-333">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="6eadb-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="6eadb-334">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="6eadb-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="6eadb-335">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="6eadb-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="6eadb-336">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="6eadb-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="6eadb-337">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="6eadb-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="6eadb-338">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="6eadb-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="6eadb-339">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="6eadb-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="6eadb-340">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-340">The PutTodoItem method</span></span>

<span data-ttu-id="6eadb-341">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6eadb-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="6eadb-342">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6eadb-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="6eadb-343">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6eadb-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="6eadb-344">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="6eadb-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="6eadb-345">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="6eadb-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="6eadb-346">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="6eadb-347">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="6eadb-348">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="6eadb-349">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6eadb-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="6eadb-350">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6eadb-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="6eadb-351">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="6eadb-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="6eadb-352">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="6eadb-352">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="6eadb-354">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="6eadb-355">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6eadb-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="6eadb-356">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="6eadb-357">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6eadb-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="6eadb-358">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="6eadb-359">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="6eadb-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="6eadb-360">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="6eadb-361">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="6eadb-361">Prevent over-posting</span></span>

<span data-ttu-id="6eadb-362">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="6eadb-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="6eadb-363">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="6eadb-364">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="6eadb-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="6eadb-365">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="6eadb-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="6eadb-366">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="6eadb-367">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="6eadb-367">A DTO may be used to:</span></span>

* <span data-ttu-id="6eadb-368">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="6eadb-368">Prevent over-posting.</span></span>
* <span data-ttu-id="6eadb-369">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="6eadb-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="6eadb-370">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="6eadb-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="6eadb-371">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="6eadb-372">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="6eadb-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="6eadb-373">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="6eadb-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="6eadb-374">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="6eadb-375">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="6eadb-376">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="6eadb-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="6eadb-377">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="6eadb-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="6eadb-378">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6eadb-379">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="6eadb-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="6eadb-380">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="6eadb-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6eadb-381">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="6eadb-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6eadb-382">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-382">Create a web API project.</span></span>
> * <span data-ttu-id="6eadb-383">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="6eadb-384">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-384">Add a controller.</span></span>
> * <span data-ttu-id="6eadb-385">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="6eadb-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="6eadb-386">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="6eadb-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="6eadb-387">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-387">Specify return values.</span></span>
> * <span data-ttu-id="6eadb-388">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="6eadb-389">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6eadb-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="6eadb-390">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="6eadb-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="6eadb-391">Visão geral</span><span class="sxs-lookup"><span data-stu-id="6eadb-391">Overview</span></span>

<span data-ttu-id="6eadb-392">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="6eadb-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="6eadb-393">API</span><span class="sxs-lookup"><span data-stu-id="6eadb-393">API</span></span> | <span data-ttu-id="6eadb-394">Descrição</span><span class="sxs-lookup"><span data-stu-id="6eadb-394">Description</span></span> | <span data-ttu-id="6eadb-395">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="6eadb-395">Request body</span></span> | <span data-ttu-id="6eadb-396">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="6eadb-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="6eadb-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6eadb-397">GET /api/TodoItems</span></span> | <span data-ttu-id="6eadb-398">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-398">Get all to-do items</span></span> | <span data-ttu-id="6eadb-399">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-399">None</span></span> | <span data-ttu-id="6eadb-400">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-400">Array of to-do items</span></span>|
|<span data-ttu-id="6eadb-401">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="6eadb-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="6eadb-402">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="6eadb-402">Get an item by ID</span></span> | <span data-ttu-id="6eadb-403">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-403">None</span></span> | <span data-ttu-id="6eadb-404">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-404">To-do item</span></span>|
|<span data-ttu-id="6eadb-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6eadb-405">POST /api/TodoItems</span></span> | <span data-ttu-id="6eadb-406">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="6eadb-406">Add a new item</span></span> | <span data-ttu-id="6eadb-407">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-407">To-do item</span></span> | <span data-ttu-id="6eadb-408">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-408">To-do item</span></span> |
|<span data-ttu-id="6eadb-409">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="6eadb-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="6eadb-410">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="6eadb-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="6eadb-411">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-411">To-do item</span></span> | <span data-ttu-id="6eadb-412">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-412">None</span></span> |
|<span data-ttu-id="6eadb-413">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="6eadb-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="6eadb-414">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="6eadb-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="6eadb-415">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-415">None</span></span> | <span data-ttu-id="6eadb-416">Nenhum</span><span class="sxs-lookup"><span data-stu-id="6eadb-416">None</span></span>|

<span data-ttu-id="6eadb-417">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-417">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="6eadb-423">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6eadb-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-426">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="6eadb-427">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="6eadb-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-429">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6eadb-430">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="6eadb-431">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="6eadb-432">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="6eadb-433">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="6eadb-434">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-434">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6eadb-437">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6eadb-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6eadb-438">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="6eadb-439">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6eadb-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="6eadb-440">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="6eadb-441">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-442">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6eadb-443">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-443">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="6eadb-445">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="6eadb-446">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="6eadb-447">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="6eadb-448">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-448">Select **Next**.</span></span>

* <span data-ttu-id="6eadb-449">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="6eadb-451">Testar a API</span><span class="sxs-lookup"><span data-stu-id="6eadb-451">Test the API</span></span>

<span data-ttu-id="6eadb-452">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-452">The project template creates a `values` API.</span></span> <span data-ttu-id="6eadb-453">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6eadb-455">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6eadb-456">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="6eadb-457">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="6eadb-458">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6eadb-460">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6eadb-461">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-462">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6eadb-463">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="6eadb-464">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="6eadb-465">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="6eadb-466">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="6eadb-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="6eadb-467">O JSON a seguir será retornado:</span><span class="sxs-lookup"><span data-stu-id="6eadb-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="6eadb-468">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="6eadb-468">Add a model class</span></span>

<span data-ttu-id="6eadb-469">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="6eadb-470">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-472">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="6eadb-473">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6eadb-474">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="6eadb-475">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6eadb-476">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="6eadb-477">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6eadb-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6eadb-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6eadb-479">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="6eadb-480">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-481">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6eadb-482">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-482">Right-click the project.</span></span> <span data-ttu-id="6eadb-483">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6eadb-484">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-484">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="6eadb-486">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="6eadb-487">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="6eadb-488">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="6eadb-489">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="6eadb-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="6eadb-490">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="6eadb-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="6eadb-491">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6eadb-491">Add a database context</span></span>

<span data-ttu-id="6eadb-492">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="6eadb-493">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-495">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6eadb-496">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-497">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6eadb-498">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="6eadb-499">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="6eadb-500">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6eadb-500">Register the database context</span></span>

<span data-ttu-id="6eadb-501">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6eadb-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6eadb-502">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="6eadb-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="6eadb-503">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="6eadb-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="6eadb-504">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6eadb-504">The preceding code:</span></span>

* <span data-ttu-id="6eadb-505">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="6eadb-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="6eadb-506">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6eadb-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="6eadb-507">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="6eadb-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6eadb-508">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="6eadb-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-510">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="6eadb-511">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="6eadb-512">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="6eadb-513">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-515">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6eadb-516">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="6eadb-517">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="6eadb-518">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6eadb-518">The preceding code:</span></span>

* <span data-ttu-id="6eadb-519">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="6eadb-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="6eadb-520">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="6eadb-521">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="6eadb-522">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="6eadb-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="6eadb-523">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="6eadb-524">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="6eadb-525">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="6eadb-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="6eadb-526">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6eadb-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="6eadb-527">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="6eadb-528">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="6eadb-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="6eadb-529">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="6eadb-529">Add Get methods</span></span>

<span data-ttu-id="6eadb-530">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="6eadb-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="6eadb-531">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="6eadb-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="6eadb-532">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="6eadb-532">Stop the app if it's still running.</span></span> <span data-ttu-id="6eadb-533">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="6eadb-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="6eadb-534">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="6eadb-535">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6eadb-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="6eadb-536">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="6eadb-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="6eadb-537">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="6eadb-537">Routing and URL paths</span></span>

<span data-ttu-id="6eadb-538">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="6eadb-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="6eadb-539">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6eadb-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="6eadb-540">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="6eadb-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="6eadb-541">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="6eadb-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="6eadb-542">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="6eadb-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="6eadb-543">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6eadb-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="6eadb-544">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="6eadb-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="6eadb-545">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-545">This sample doesn't use a template.</span></span> <span data-ttu-id="6eadb-546">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6eadb-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6eadb-547">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="6eadb-548">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="6eadb-549">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="6eadb-549">Return values</span></span>

<span data-ttu-id="6eadb-550">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="6eadb-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="6eadb-551">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="6eadb-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="6eadb-552">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="6eadb-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="6eadb-553">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="6eadb-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="6eadb-554">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="6eadb-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="6eadb-555">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="6eadb-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="6eadb-556">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="6eadb-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="6eadb-557">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="6eadb-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="6eadb-558">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="6eadb-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="6eadb-559">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="6eadb-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="6eadb-560">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="6eadb-561">Instalar o [postmaster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="6eadb-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="6eadb-562">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-562">Start the web app.</span></span>
* <span data-ttu-id="6eadb-563">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-563">Start Postman.</span></span>
* <span data-ttu-id="6eadb-564">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eadb-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eadb-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6eadb-566">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6eadb-567">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6eadb-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6eadb-568">De **Postman**  >  **preferências** do postmaster (guia**geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="6eadb-569">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="6eadb-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="6eadb-570">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="6eadb-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="6eadb-571">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6eadb-571">Create a new request.</span></span>
  * <span data-ttu-id="6eadb-572">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="6eadb-573">Defina o URI de solicitação como `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="6eadb-574">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="6eadb-575">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="6eadb-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="6eadb-576">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-576">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="6eadb-578">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="6eadb-578">Add a Create method</span></span>

<span data-ttu-id="6eadb-579">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6eadb-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="6eadb-580">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6eadb-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="6eadb-581">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6eadb-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="6eadb-582">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="6eadb-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="6eadb-583">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="6eadb-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="6eadb-584">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="6eadb-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="6eadb-585">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="6eadb-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="6eadb-586">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="6eadb-587">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6eadb-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="6eadb-588">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="6eadb-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="6eadb-589">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="6eadb-590">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="6eadb-591">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-591">Build the project.</span></span>
* <span data-ttu-id="6eadb-592">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="6eadb-593">Defina o URI como `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="6eadb-594">Por exemplo, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="6eadb-595">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="6eadb-596">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="6eadb-597">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="6eadb-598">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6eadb-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="6eadb-599">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-599">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="6eadb-601">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="6eadb-602">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="6eadb-602">Test the location header URI</span></span>

* <span data-ttu-id="6eadb-603">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="6eadb-604">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="6eadb-604">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="6eadb-606">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="6eadb-606">Set the method to GET.</span></span>
<span data-ttu-id="6eadb-607">\* Defina o URI como  `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="6eadb-608">Por exemplo,  `https://localhost:5001/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="6eadb-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="6eadb-609">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="6eadb-610">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="6eadb-611">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="6eadb-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="6eadb-612">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6eadb-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="6eadb-613">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6eadb-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="6eadb-614">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="6eadb-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="6eadb-615">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="6eadb-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="6eadb-616">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6eadb-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="6eadb-617">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="6eadb-618">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="6eadb-619">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6eadb-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="6eadb-620">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6eadb-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="6eadb-621">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="6eadb-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="6eadb-622">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="6eadb-622">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="6eadb-624">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="6eadb-625">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="6eadb-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="6eadb-626">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6eadb-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="6eadb-627">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6eadb-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="6eadb-628">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6eadb-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="6eadb-629">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="6eadb-630">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="6eadb-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="6eadb-631">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6eadb-631">Select **Send**.</span></span>

<span data-ttu-id="6eadb-632">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="6eadb-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="6eadb-633">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="6eadb-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6eadb-634">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="6eadb-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="6eadb-635">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="6eadb-636">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="6eadb-636">jQuery initiates the request.</span></span> <span data-ttu-id="6eadb-637">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="6eadb-638">Configurar o aplicativo para [servir arquivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) e [habilitar o mapeamento de arquivo padrão](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="6eadb-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="6eadb-639">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="6eadb-640">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="6eadb-641">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="6eadb-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="6eadb-642">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="6eadb-643">Substitua o conteúdo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6eadb-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="6eadb-644">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="6eadb-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="6eadb-645">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6eadb-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="6eadb-646">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="6eadb-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="6eadb-647">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="6eadb-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="6eadb-648">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="6eadb-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="6eadb-649">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="6eadb-649">Get a list of to-do items</span></span>

<span data-ttu-id="6eadb-650">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="6eadb-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="6eadb-651">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="6eadb-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="6eadb-652">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="6eadb-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="6eadb-653">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="6eadb-653">Add a to-do item</span></span>

<span data-ttu-id="6eadb-654">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6eadb-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="6eadb-655">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="6eadb-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="6eadb-656">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="6eadb-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="6eadb-657">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="6eadb-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="6eadb-658">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="6eadb-658">Update a to-do item</span></span>

<span data-ttu-id="6eadb-659">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="6eadb-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="6eadb-660">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="6eadb-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="6eadb-661">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="6eadb-661">Delete a to-do item</span></span>

<span data-ttu-id="6eadb-662">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="6eadb-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="6eadb-663">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="6eadb-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="6eadb-664">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6eadb-664">Additional resources</span></span>

<span data-ttu-id="6eadb-665">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="6eadb-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="6eadb-666">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="6eadb-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="6eadb-667">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6eadb-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="6eadb-668">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="6eadb-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
