---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- ':::no-loc(Models):::'
uid: tutorials/first-web-api
ms.openlocfilehash: ab06f2d347d7f05e9c72a85e0c6e0fbc67fe48a7
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550706"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="739a0-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="739a0-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="739a0-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="739a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="739a0-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="739a0-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="739a0-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="739a0-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="739a0-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-107">Create a web API project.</span></span>
> * <span data-ttu-id="739a0-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="739a0-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="739a0-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="739a0-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="739a0-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="739a0-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-111">Call the web API with Postman.</span></span>

<span data-ttu-id="739a0-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="739a0-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="739a0-113">Overview</span></span>

<span data-ttu-id="739a0-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="739a0-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="739a0-115">API</span><span class="sxs-lookup"><span data-stu-id="739a0-115">API</span></span> | <span data-ttu-id="739a0-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="739a0-116">Description</span></span> | <span data-ttu-id="739a0-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="739a0-117">Request body</span></span> | <span data-ttu-id="739a0-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="739a0-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="739a0-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-119">Get all to-do items</span></span> | <span data-ttu-id="739a0-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-120">None</span></span> | <span data-ttu-id="739a0-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="739a0-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="739a0-122">Get an item by ID</span></span> | <span data-ttu-id="739a0-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-123">None</span></span> | <span data-ttu-id="739a0-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="739a0-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="739a0-125">Add a new item</span></span> | <span data-ttu-id="739a0-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-126">To-do item</span></span> | <span data-ttu-id="739a0-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="739a0-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="739a0-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="739a0-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-129">To-do item</span></span> | <span data-ttu-id="739a0-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-130">None</span></span> |
|<span data-ttu-id="739a0-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="739a0-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="739a0-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="739a0-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="739a0-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-133">None</span></span> | <span data-ttu-id="739a0-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-134">None</span></span>|

<span data-ttu-id="739a0-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="739a0-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="739a0-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="739a0-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="739a0-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-147">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="739a0-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="739a0-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="739a0-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="739a0-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 5,0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="739a0-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="739a0-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="739a0-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="739a0-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="739a0-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="739a0-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="739a0-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="739a0-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="739a0-158">The preceding commands:</span></span>

  * <span data-ttu-id="739a0-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="739a0-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="739a0-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="739a0-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="739a0-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="739a0-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="739a0-164">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="739a0-165">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="739a0-167">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 5. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="739a0-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="739a0-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-168">Select **Next**.</span></span>

* <span data-ttu-id="739a0-169">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="739a0-171">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="739a0-172">Testar o projeto</span><span class="sxs-lookup"><span data-stu-id="739a0-172">Test the project</span></span>

<span data-ttu-id="739a0-173">O modelo de projeto cria uma `WeatherForecast` API com suporte para [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="739a0-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="739a0-175">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="739a0-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="739a0-176">O Visual Studio é iniciado:</span><span class="sxs-lookup"><span data-stu-id="739a0-176">Visual Studio launches:</span></span>

* <span data-ttu-id="739a0-177">O servidor Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="739a0-177">The IIS Express web server.</span></span>
* <span data-ttu-id="739a0-178">O navegador padrão e navega para `https://localhost:<port>/https://localhost:5001/swagger/index.html` , em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="739a0-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="739a0-180">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="739a0-181">Em um navegador, vá para a seguinte URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="739a0-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-182">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="739a0-183">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="739a0-184">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="739a0-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="739a0-185">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="739a0-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="739a0-186">Acrescente `/swagger` à URL (altere a URL para `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="739a0-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="739a0-187">A página Swagger `/swagger/index.html` é exibida.</span><span class="sxs-lookup"><span data-stu-id="739a0-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="739a0-188">Selecione **Get**  >  **Experimente**  >  **executar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="739a0-189">A página exibe:</span><span class="sxs-lookup"><span data-stu-id="739a0-189">The page displays:</span></span>

* <span data-ttu-id="739a0-190">O comando de [ondulação](https://curl.haxx.se/) para testar a API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="739a0-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="739a0-191">A URL para testar a API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="739a0-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="739a0-192">O código de resposta, o corpo e os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="739a0-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="739a0-193">Uma caixa de listagem suspensa com tipos de mídia e o valor e o esquema de exemplo.</span><span class="sxs-lookup"><span data-stu-id="739a0-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="739a0-194">O Swagger é usado para gerar documentação útil e páginas de ajuda para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="739a0-195">Este tutorial se concentra na criação de uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="739a0-196">Para obter mais informações sobre o Swagger, consulte <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="739a0-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="739a0-197">Copie e cole a **URL de solicitação** no navegador:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="739a0-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="739a0-198">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="739a0-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="739a0-199">Atualizar o launchUrl</span><span class="sxs-lookup"><span data-stu-id="739a0-199">Update the launchUrl</span></span>

<span data-ttu-id="739a0-200">No *Properties\launchSettings.jsem* , atualize `launchUrl` do `"swagger"` para `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="739a0-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="739a0-201">Como o Swagger foi removido, a marcação anterior altera a URL que é iniciada para o método GET do controlador adicionado nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="739a0-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="739a0-202">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="739a0-202">Add a model class</span></span>

<span data-ttu-id="739a0-203">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="739a0-204">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="739a0-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-206">Em **Gerenciador de soluções** , clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="739a0-207">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="739a0-208">Nomeie a pasta *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-208">Name the folder *:::no-loc(Models):::*.</span></span>

* <span data-ttu-id="739a0-209">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="739a0-209">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="739a0-210">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="739a0-211">Substitua o código do modelo pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="739a0-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="739a0-213">Adicione uma pasta chamada *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-213">Add a folder named *:::no-loc(Models):::*.</span></span>

* <span data-ttu-id="739a0-214">Adicione uma `TodoItem` classe à *:::no-loc(Models):::* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-214">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="739a0-216">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-216">Right-click the project.</span></span> <span data-ttu-id="739a0-217">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="739a0-218">Nomeie a pasta *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-218">Name the folder *:::no-loc(Models):::*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="739a0-220">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar** > **novo arquivo** > **General** > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="739a0-220">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="739a0-221">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-221">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="739a0-222">Substitua o código do modelo pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="739a0-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="739a0-223">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="739a0-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="739a0-224">As classes de modelo podem ir em qualquer lugar no projeto, mas a *:::no-loc(Models):::* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="739a0-224">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="739a0-225">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="739a0-225">Add a database context</span></span>

<span data-ttu-id="739a0-226">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="739a0-227">Essa classe é criada derivando-a da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="739a0-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="739a0-229">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="739a0-229">Add NuGet packages</span></span>

* <span data-ttu-id="739a0-230">No menu **Ferramentas** , selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="739a0-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="739a0-231">Selecione a guia **procurar** e, em seguida, insira \* \* Microsoft.</span><span class="sxs-lookup"><span data-stu-id="739a0-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="739a0-232">**EntityFrameworkCore. SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="739a0-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="739a0-233">Marque a caixa de seleção **incluir pré-lançamento** para que a versão 5,0 RC esteja disponível.</span><span class="sxs-lookup"><span data-stu-id="739a0-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="739a0-234">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="739a0-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="739a0-235">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="739a0-236">Use as instruções anteriores para adicionar o pacote NuGet **Microsoft. EntityFrameworkCore. inmemory** .</span><span class="sxs-lookup"><span data-stu-id="739a0-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="739a0-237">![Gerenciador de Pacotes do NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="739a0-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="739a0-238">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="739a0-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="739a0-239">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="739a0-239">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="739a0-240">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-241">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="739a0-242">Adicione uma `TodoContext` classe à *:::no-loc(Models):::* pasta.</span><span class="sxs-lookup"><span data-stu-id="739a0-242">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="739a0-243">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="739a0-244">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="739a0-244">Register the database context</span></span>

<span data-ttu-id="739a0-245">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="739a0-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="739a0-246">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="739a0-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="739a0-247">Atualize *Startup.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="739a0-248">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="739a0-248">The preceding code:</span></span>

* <span data-ttu-id="739a0-249">Remove as chamadas do Swagger.</span><span class="sxs-lookup"><span data-stu-id="739a0-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="739a0-250">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="739a0-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="739a0-251">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="739a0-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="739a0-252">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="739a0-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="739a0-253">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="739a0-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-255">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="739a0-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="739a0-256">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="739a0-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="739a0-257">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-257">Select **API Controller with actions, using Entity Framework** , and then select **Add**.</span></span>
* <span data-ttu-id="739a0-258">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="739a0-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="739a0-259">Selecione **TodoItem (TodoApi. :::no-loc(Models)::: )** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="739a0-259">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class**.</span></span>
  * <span data-ttu-id="739a0-260">Selecione **TodoContext (TodoApi. :::no-loc(Models)::: )** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="739a0-260">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class**.</span></span>
  * <span data-ttu-id="739a0-261">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-262">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="739a0-263">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="739a0-264">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="739a0-264">The preceding commands:</span></span>

* <span data-ttu-id="739a0-265">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="739a0-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="739a0-266">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="739a0-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="739a0-267">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="739a0-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="739a0-268">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="739a0-268">The generated code:</span></span>

* <span data-ttu-id="739a0-269">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="739a0-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="739a0-270">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="739a0-271">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="739a0-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="739a0-272">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="739a0-273">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="739a0-274">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="739a0-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="739a0-275">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="739a0-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="739a0-276">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="739a0-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="739a0-277">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="739a0-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="739a0-278">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="739a0-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="739a0-279">Atualizar o método de criação de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="739a0-280">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="739a0-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="739a0-281">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="739a0-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="739a0-282">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="739a0-283">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="739a0-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="739a0-284">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="739a0-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="739a0-285">Retorna um [código de status HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) se for bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="739a0-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="739a0-286">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="739a0-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="739a0-287">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="739a0-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="739a0-288">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="739a0-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="739a0-289">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="739a0-290">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="739a0-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="739a0-291">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="739a0-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="739a0-292">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="739a0-292">Install Postman</span></span>

<span data-ttu-id="739a0-293">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="739a0-294">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="739a0-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="739a0-295">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-295">Start the web app.</span></span>
* <span data-ttu-id="739a0-296">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-296">Start Postman.</span></span>
* <span data-ttu-id="739a0-297">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="739a0-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="739a0-298">Em **Arquivo** > **Configurações** (guia **Geral** ), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="739a0-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="739a0-299">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="739a0-300">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="739a0-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="739a0-301">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-301">Create a new request.</span></span>
* <span data-ttu-id="739a0-302">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="739a0-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="739a0-303">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="739a0-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="739a0-304">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="739a0-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="739a0-305">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="739a0-306">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="739a0-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="739a0-307">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="739a0-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="739a0-308">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="739a0-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="739a0-309">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-309">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="739a0-311">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="739a0-311">Test the location header URI</span></span>

<span data-ttu-id="739a0-312">O URI do cabeçalho do local pode ser testado no navegador.</span><span class="sxs-lookup"><span data-stu-id="739a0-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="739a0-313">Copie e cole o URI do cabeçalho de local no navegador.</span><span class="sxs-lookup"><span data-stu-id="739a0-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="739a0-314">Para testar no postmaster:</span><span class="sxs-lookup"><span data-stu-id="739a0-314">To test in Postman:</span></span>

* <span data-ttu-id="739a0-315">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="739a0-316">Copie o valor do cabeçalho **Local** :</span><span class="sxs-lookup"><span data-stu-id="739a0-316">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="739a0-318">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="739a0-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="739a0-319">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="739a0-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="739a0-320">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="739a0-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="739a0-321">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="739a0-322">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="739a0-322">Examine the GET methods</span></span>

<span data-ttu-id="739a0-323">Dois pontos de extremidade GET são implementados:</span><span class="sxs-lookup"><span data-stu-id="739a0-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="739a0-324">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="739a0-325">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="739a0-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="739a0-326">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="739a0-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="739a0-327">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="739a0-327">Test Get with Postman</span></span>

* <span data-ttu-id="739a0-328">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-328">Create a new request.</span></span>
* <span data-ttu-id="739a0-329">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="739a0-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="739a0-330">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="739a0-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="739a0-331">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="739a0-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="739a0-332">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="739a0-333">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-333">Select **Send**.</span></span>

<span data-ttu-id="739a0-334">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="739a0-334">This app uses an in-memory database.</span></span> <span data-ttu-id="739a0-335">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="739a0-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="739a0-336">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="739a0-337">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="739a0-337">Routing and URL paths</span></span>

<span data-ttu-id="739a0-338">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="739a0-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="739a0-339">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="739a0-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="739a0-340">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="739a0-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="739a0-341">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="739a0-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="739a0-342">Para esta amostra, o nome da classe do controlador é **TodoItems** Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="739a0-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="739a0-343">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="739a0-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="739a0-344">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="739a0-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="739a0-345">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="739a0-345">This sample doesn't use a template.</span></span> <span data-ttu-id="739a0-346">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="739a0-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="739a0-347">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="739a0-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="739a0-348">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="739a0-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="739a0-349">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="739a0-349">Return values</span></span>

<span data-ttu-id="739a0-350">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="739a0-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="739a0-351">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="739a0-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="739a0-352">O código de resposta para esse tipo de retorno é [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="739a0-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="739a0-353">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="739a0-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="739a0-354">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="739a0-355">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="739a0-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="739a0-356">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro de [status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="739a0-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="739a0-357">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="739a0-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="739a0-358">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="739a0-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="739a0-359">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-359">The PutTodoItem method</span></span>

<span data-ttu-id="739a0-360">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="739a0-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="739a0-361">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="739a0-362">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="739a0-363">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="739a0-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="739a0-364">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="739a0-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="739a0-365">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="739a0-366">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="739a0-367">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="739a0-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="739a0-368">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="739a0-369">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="739a0-370">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="739a0-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="739a0-371">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="739a0-371">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="739a0-373">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="739a0-374">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="739a0-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="739a0-375">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="739a0-376">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="739a0-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="739a0-377">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="739a0-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="739a0-378">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="739a0-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="739a0-379">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-379">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="739a0-380">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="739a0-380">Prevent over-posting</span></span>

<span data-ttu-id="739a0-381">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="739a0-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="739a0-382">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="739a0-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="739a0-383">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="739a0-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="739a0-384">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="739a0-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="739a0-385">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="739a0-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="739a0-386">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="739a0-386">A DTO may be used to:</span></span>

* <span data-ttu-id="739a0-387">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="739a0-387">Prevent over-posting.</span></span>
* <span data-ttu-id="739a0-388">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="739a0-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="739a0-389">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="739a0-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="739a0-390">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="739a0-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="739a0-391">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="739a0-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="739a0-392">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="739a0-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="739a0-393">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="739a0-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="739a0-394">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="739a0-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="739a0-395">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="739a0-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="739a0-396">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="739a0-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="739a0-397">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="739a0-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="739a0-398">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="739a0-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="739a0-399">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="739a0-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="739a0-400">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="739a0-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="739a0-401">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-401">Create a web API project.</span></span>
> * <span data-ttu-id="739a0-402">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="739a0-403">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="739a0-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="739a0-404">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="739a0-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="739a0-405">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-405">Call the web API with Postman.</span></span>

<span data-ttu-id="739a0-406">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="739a0-407">Visão geral</span><span class="sxs-lookup"><span data-stu-id="739a0-407">Overview</span></span>

<span data-ttu-id="739a0-408">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="739a0-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="739a0-409">API</span><span class="sxs-lookup"><span data-stu-id="739a0-409">API</span></span> | <span data-ttu-id="739a0-410">Descrição</span><span class="sxs-lookup"><span data-stu-id="739a0-410">Description</span></span> | <span data-ttu-id="739a0-411">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="739a0-411">Request body</span></span> | <span data-ttu-id="739a0-412">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="739a0-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="739a0-413">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-413">Get all to-do items</span></span> | <span data-ttu-id="739a0-414">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-414">None</span></span> | <span data-ttu-id="739a0-415">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="739a0-416">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="739a0-416">Get an item by ID</span></span> | <span data-ttu-id="739a0-417">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-417">None</span></span> | <span data-ttu-id="739a0-418">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="739a0-419">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="739a0-419">Add a new item</span></span> | <span data-ttu-id="739a0-420">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-420">To-do item</span></span> | <span data-ttu-id="739a0-421">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="739a0-422">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="739a0-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="739a0-423">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-423">To-do item</span></span> | <span data-ttu-id="739a0-424">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-424">None</span></span> |
|<span data-ttu-id="739a0-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="739a0-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="739a0-426">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="739a0-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="739a0-427">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-427">None</span></span> | <span data-ttu-id="739a0-428">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-428">None</span></span>|

<span data-ttu-id="739a0-429">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-429">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="739a0-435">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="739a0-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="739a0-439">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="739a0-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-441">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="739a0-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="739a0-442">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="739a0-443">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="739a0-444">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="739a0-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="739a0-445">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-445">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="739a0-448">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="739a0-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="739a0-449">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="739a0-450">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="739a0-451">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="739a0-452">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="739a0-452">The preceding commands:</span></span>

  * <span data-ttu-id="739a0-453">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="739a0-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="739a0-454">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="739a0-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-455">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="739a0-456">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="739a0-456">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="739a0-458">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="739a0-459">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="739a0-461">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="739a0-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="739a0-462">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-462">Select **Next**.</span></span>

* <span data-ttu-id="739a0-463">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="739a0-465">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="739a0-466">Testar a API</span><span class="sxs-lookup"><span data-stu-id="739a0-466">Test the API</span></span>

<span data-ttu-id="739a0-467">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="739a0-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="739a0-468">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="739a0-470">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="739a0-471">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="739a0-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="739a0-472">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="739a0-473">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="739a0-475">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="739a0-476">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="739a0-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-477">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="739a0-478">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="739a0-479">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="739a0-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="739a0-480">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="739a0-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="739a0-481">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="739a0-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="739a0-482">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="739a0-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="739a0-483">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="739a0-483">Add a model class</span></span>

<span data-ttu-id="739a0-484">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="739a0-485">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="739a0-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-487">Em **Gerenciador de soluções** , clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="739a0-488">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="739a0-489">Nomeie a pasta *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-489">Name the folder *:::no-loc(Models):::*.</span></span>

* <span data-ttu-id="739a0-490">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="739a0-490">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="739a0-491">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="739a0-492">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="739a0-494">Adicione uma pasta chamada *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-494">Add a folder named *:::no-loc(Models):::*.</span></span>

* <span data-ttu-id="739a0-495">Adicione uma `TodoItem` classe à *:::no-loc(Models):::* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-495">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-496">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="739a0-497">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-497">Right-click the project.</span></span> <span data-ttu-id="739a0-498">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="739a0-499">Nomeie a pasta *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-499">Name the folder *:::no-loc(Models):::*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="739a0-501">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar** > **novo arquivo** > **General** > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="739a0-501">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="739a0-502">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-502">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="739a0-503">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="739a0-504">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="739a0-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="739a0-505">As classes de modelo podem ir em qualquer lugar no projeto, mas a *:::no-loc(Models):::* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="739a0-505">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="739a0-506">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="739a0-506">Add a database context</span></span>

<span data-ttu-id="739a0-507">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="739a0-508">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="739a0-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="739a0-510">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="739a0-510">Add NuGet packages</span></span>

* <span data-ttu-id="739a0-511">No menu **Ferramentas** , selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="739a0-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="739a0-512">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="739a0-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="739a0-513">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="739a0-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="739a0-514">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="739a0-515">Use as instruções anteriores para adicionar o pacote NuGet **Microsoft. EntityFrameworkCore. inmemory** .</span><span class="sxs-lookup"><span data-stu-id="739a0-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="739a0-517">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="739a0-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="739a0-518">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="739a0-518">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="739a0-519">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-520">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="739a0-521">Adicione uma `TodoContext` classe à *:::no-loc(Models):::* pasta.</span><span class="sxs-lookup"><span data-stu-id="739a0-521">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="739a0-522">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="739a0-523">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="739a0-523">Register the database context</span></span>

<span data-ttu-id="739a0-524">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="739a0-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="739a0-525">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="739a0-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="739a0-526">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="739a0-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="739a0-527">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="739a0-527">The preceding code:</span></span>

* <span data-ttu-id="739a0-528">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="739a0-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="739a0-529">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="739a0-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="739a0-530">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="739a0-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="739a0-531">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="739a0-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-533">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="739a0-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="739a0-534">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="739a0-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="739a0-535">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-535">Select **API Controller with actions, using Entity Framework** , and then select **Add**.</span></span>
* <span data-ttu-id="739a0-536">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="739a0-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="739a0-537">Selecione **TodoItem (TodoApi. :::no-loc(Models)::: )** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="739a0-537">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class**.</span></span>
  * <span data-ttu-id="739a0-538">Selecione **TodoContext (TodoApi. :::no-loc(Models)::: )** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="739a0-538">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class**.</span></span>
  * <span data-ttu-id="739a0-539">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-540">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="739a0-541">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="739a0-542">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="739a0-542">The preceding commands:</span></span>

* <span data-ttu-id="739a0-543">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="739a0-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="739a0-544">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="739a0-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="739a0-545">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="739a0-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="739a0-546">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="739a0-546">The generated code:</span></span>

* <span data-ttu-id="739a0-547">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="739a0-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="739a0-548">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="739a0-549">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="739a0-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="739a0-550">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="739a0-551">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="739a0-552">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="739a0-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="739a0-553">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="739a0-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="739a0-554">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="739a0-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="739a0-555">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="739a0-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="739a0-556">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="739a0-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="739a0-557">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="739a0-558">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="739a0-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="739a0-559">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="739a0-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="739a0-560">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="739a0-561">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="739a0-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="739a0-562">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="739a0-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="739a0-563">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="739a0-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="739a0-564">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="739a0-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="739a0-565">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="739a0-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="739a0-566">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="739a0-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="739a0-567">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="739a0-568">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="739a0-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="739a0-569">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="739a0-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="739a0-570">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="739a0-570">Install Postman</span></span>

<span data-ttu-id="739a0-571">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="739a0-572">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="739a0-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="739a0-573">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-573">Start the web app.</span></span>
* <span data-ttu-id="739a0-574">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-574">Start Postman.</span></span>
* <span data-ttu-id="739a0-575">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="739a0-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="739a0-576">Em **Arquivo** > **Configurações** (guia **Geral** ), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="739a0-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="739a0-577">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="739a0-578">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="739a0-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="739a0-579">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-579">Create a new request.</span></span>
* <span data-ttu-id="739a0-580">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="739a0-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="739a0-581">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="739a0-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="739a0-582">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="739a0-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="739a0-583">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="739a0-584">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="739a0-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="739a0-585">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="739a0-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="739a0-586">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="739a0-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="739a0-587">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-587">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="739a0-589">Testar o URI do cabeçalho do local com o postmaster</span><span class="sxs-lookup"><span data-stu-id="739a0-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="739a0-590">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="739a0-591">Copie o valor do cabeçalho **Local** :</span><span class="sxs-lookup"><span data-stu-id="739a0-591">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="739a0-593">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="739a0-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="739a0-594">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="739a0-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="739a0-595">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="739a0-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="739a0-596">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="739a0-597">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="739a0-597">Examine the GET methods</span></span>

<span data-ttu-id="739a0-598">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="739a0-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="739a0-599">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="739a0-600">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="739a0-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="739a0-601">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="739a0-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="739a0-602">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="739a0-602">Test Get with Postman</span></span>

* <span data-ttu-id="739a0-603">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-603">Create a new request.</span></span>
* <span data-ttu-id="739a0-604">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="739a0-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="739a0-605">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="739a0-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="739a0-606">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="739a0-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="739a0-607">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="739a0-608">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-608">Select **Send**.</span></span>

<span data-ttu-id="739a0-609">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="739a0-609">This app uses an in-memory database.</span></span> <span data-ttu-id="739a0-610">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="739a0-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="739a0-611">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="739a0-612">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="739a0-612">Routing and URL paths</span></span>

<span data-ttu-id="739a0-613">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="739a0-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="739a0-614">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="739a0-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="739a0-615">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="739a0-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="739a0-616">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="739a0-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="739a0-617">Para esta amostra, o nome da classe do controlador é **TodoItems** Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="739a0-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="739a0-618">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="739a0-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="739a0-619">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="739a0-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="739a0-620">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="739a0-620">This sample doesn't use a template.</span></span> <span data-ttu-id="739a0-621">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="739a0-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="739a0-622">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="739a0-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="739a0-623">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="739a0-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="739a0-624">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="739a0-624">Return values</span></span> 

<span data-ttu-id="739a0-625">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="739a0-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="739a0-626">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="739a0-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="739a0-627">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="739a0-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="739a0-628">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="739a0-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="739a0-629">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="739a0-630">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="739a0-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="739a0-631">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="739a0-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="739a0-632">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="739a0-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="739a0-633">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="739a0-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="739a0-634">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-634">The PutTodoItem method</span></span>

<span data-ttu-id="739a0-635">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="739a0-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="739a0-636">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="739a0-637">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="739a0-638">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="739a0-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="739a0-639">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="739a0-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="739a0-640">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="739a0-641">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="739a0-642">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="739a0-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="739a0-643">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="739a0-644">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="739a0-645">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="739a0-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="739a0-646">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="739a0-646">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="739a0-648">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="739a0-649">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="739a0-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="739a0-650">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="739a0-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="739a0-651">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="739a0-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="739a0-652">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="739a0-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="739a0-653">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="739a0-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="739a0-654">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-654">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="739a0-655">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="739a0-655">Prevent over-posting</span></span>

<span data-ttu-id="739a0-656">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="739a0-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="739a0-657">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="739a0-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="739a0-658">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="739a0-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="739a0-659">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="739a0-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="739a0-660">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="739a0-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="739a0-661">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="739a0-661">A DTO may be used to:</span></span>

* <span data-ttu-id="739a0-662">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="739a0-662">Prevent over-posting.</span></span>
* <span data-ttu-id="739a0-663">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="739a0-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="739a0-664">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="739a0-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="739a0-665">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="739a0-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="739a0-666">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="739a0-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="739a0-667">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="739a0-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="739a0-668">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="739a0-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="739a0-669">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="739a0-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="739a0-670">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="739a0-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="739a0-671">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="739a0-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="739a0-672">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="739a0-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="739a0-673">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="739a0-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="739a0-674">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="739a0-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="739a0-675">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="739a0-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="739a0-676">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-676">Create a web API project.</span></span>
> * <span data-ttu-id="739a0-677">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="739a0-678">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-678">Add a controller.</span></span>
> * <span data-ttu-id="739a0-679">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="739a0-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="739a0-680">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="739a0-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="739a0-681">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="739a0-681">Specify return values.</span></span>
> * <span data-ttu-id="739a0-682">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="739a0-683">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="739a0-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="739a0-684">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="739a0-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="739a0-685">Visão geral 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-685">Overview 2.1</span></span>

<span data-ttu-id="739a0-686">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="739a0-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="739a0-687">API</span><span class="sxs-lookup"><span data-stu-id="739a0-687">API</span></span> | <span data-ttu-id="739a0-688">Descrição</span><span class="sxs-lookup"><span data-stu-id="739a0-688">Description</span></span> | <span data-ttu-id="739a0-689">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="739a0-689">Request body</span></span> | <span data-ttu-id="739a0-690">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="739a0-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="739a0-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="739a0-691">GET /api/TodoItems</span></span> | <span data-ttu-id="739a0-692">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-692">Get all to-do items</span></span> | <span data-ttu-id="739a0-693">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-693">None</span></span> | <span data-ttu-id="739a0-694">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-694">Array of to-do items</span></span>|
|<span data-ttu-id="739a0-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="739a0-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="739a0-696">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="739a0-696">Get an item by ID</span></span> | <span data-ttu-id="739a0-697">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-697">None</span></span> | <span data-ttu-id="739a0-698">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-698">To-do item</span></span>|
|<span data-ttu-id="739a0-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="739a0-699">POST /api/TodoItems</span></span> | <span data-ttu-id="739a0-700">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="739a0-700">Add a new item</span></span> | <span data-ttu-id="739a0-701">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-701">To-do item</span></span> | <span data-ttu-id="739a0-702">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-702">To-do item</span></span> |
|<span data-ttu-id="739a0-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="739a0-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="739a0-704">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="739a0-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="739a0-705">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="739a0-705">To-do item</span></span> | <span data-ttu-id="739a0-706">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-706">None</span></span> |
|<span data-ttu-id="739a0-707">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="739a0-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="739a0-708">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="739a0-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="739a0-709">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-709">None</span></span> | <span data-ttu-id="739a0-710">Nenhum</span><span class="sxs-lookup"><span data-stu-id="739a0-710">None</span></span>|

<span data-ttu-id="739a0-711">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-711">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="739a0-717">Pré-requisitos 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-720">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="739a0-721">Criar um projeto Web 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-723">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="739a0-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="739a0-724">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="739a0-725">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="739a0-726">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core** , confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="739a0-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="739a0-727">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="739a0-728">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="739a0-728">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="739a0-731">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="739a0-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="739a0-732">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="739a0-733">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="739a0-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="739a0-734">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="739a0-735">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-736">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="739a0-737">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="739a0-737">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="739a0-739">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="739a0-740">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="739a0-741">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="739a0-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="739a0-742">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-742">Select **Next**.</span></span>

* <span data-ttu-id="739a0-743">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="739a0-745">Testar a API 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-745">Test the API 2.1</span></span>

<span data-ttu-id="739a0-746">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="739a0-746">The project template creates a `values` API.</span></span> <span data-ttu-id="739a0-747">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="739a0-749">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="739a0-750">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="739a0-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="739a0-751">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="739a0-752">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="739a0-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="739a0-754">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="739a0-755">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="739a0-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-756">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="739a0-757">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="739a0-758">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="739a0-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="739a0-759">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="739a0-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="739a0-760">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="739a0-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="739a0-761">O JSON a seguir será retornado:</span><span class="sxs-lookup"><span data-stu-id="739a0-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="739a0-762">Adicionar uma classe de modelo 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-762">Add a model class 2.1</span></span>

<span data-ttu-id="739a0-763">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="739a0-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="739a0-764">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="739a0-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-766">Em **Gerenciador de soluções** , clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="739a0-767">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="739a0-768">Nomeie a pasta *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-768">Name the folder *:::no-loc(Models):::*.</span></span>

* <span data-ttu-id="739a0-769">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="739a0-769">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="739a0-770">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="739a0-771">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="739a0-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="739a0-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="739a0-773">Adicione uma pasta chamada *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-773">Add a folder named *:::no-loc(Models):::*.</span></span>

* <span data-ttu-id="739a0-774">Adicione uma `TodoItem` classe à *:::no-loc(Models):::* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-774">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="739a0-775">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="739a0-776">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-776">Right-click the project.</span></span> <span data-ttu-id="739a0-777">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="739a0-778">Nomeie a pasta *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="739a0-778">Name the folder *:::no-loc(Models):::*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="739a0-780">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar** > **novo arquivo** > **General** > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="739a0-780">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="739a0-781">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-781">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="739a0-782">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoItem.cs)]

<span data-ttu-id="739a0-783">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="739a0-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="739a0-784">As classes de modelo podem ir em qualquer lugar no projeto, mas a *:::no-loc(Models):::* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="739a0-784">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="739a0-785">Adicionar um contexto de banco de dados 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-785">Add a database context 2.1</span></span>

<span data-ttu-id="739a0-786">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="739a0-787">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="739a0-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-789">Clique com o botão direito do mouse na *:::no-loc(Models):::* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="739a0-789">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="739a0-790">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-791">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="739a0-792">Adicione uma `TodoContext` classe à *:::no-loc(Models):::* pasta.</span><span class="sxs-lookup"><span data-stu-id="739a0-792">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="739a0-793">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="739a0-794">Registrar o contexto de banco de dados 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-794">Register the database context 2.1</span></span>

<span data-ttu-id="739a0-795">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="739a0-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="739a0-796">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="739a0-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="739a0-797">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="739a0-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="739a0-798">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="739a0-798">The preceding code:</span></span>

* <span data-ttu-id="739a0-799">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="739a0-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="739a0-800">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="739a0-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="739a0-801">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="739a0-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="739a0-802">Adicionar um controlador 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-804">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="739a0-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="739a0-805">Selecione **Adicionar** > **novo item**.</span><span class="sxs-lookup"><span data-stu-id="739a0-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="739a0-806">Na caixa de diálogo **Adicionar Novo Item** , selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="739a0-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="739a0-807">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-807">Name the class *TodoController* , and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-809">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="739a0-810">Na pasta *Controllers* , crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="739a0-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="739a0-811">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="739a0-812">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="739a0-812">The preceding code:</span></span>

* <span data-ttu-id="739a0-813">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="739a0-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="739a0-814">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="739a0-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="739a0-815">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="739a0-816">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="739a0-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="739a0-817">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="739a0-818">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="739a0-819">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="739a0-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="739a0-820">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="739a0-821">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="739a0-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="739a0-822">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="739a0-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="739a0-823">Adicionar os métodos get 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-823">Add Get methods 2.1</span></span>

<span data-ttu-id="739a0-824">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="739a0-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="739a0-825">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="739a0-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="739a0-826">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="739a0-826">Stop the app if it's still running.</span></span> <span data-ttu-id="739a0-827">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="739a0-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="739a0-828">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="739a0-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="739a0-829">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="739a0-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="739a0-830">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="739a0-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="739a0-831">Caminhos de roteamento e URL 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="739a0-832">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="739a0-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="739a0-833">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="739a0-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="739a0-834">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="739a0-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="739a0-835">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="739a0-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="739a0-836">Para esta amostra, o nome da classe do controlador é **Todo** Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="739a0-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="739a0-837">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="739a0-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="739a0-838">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="739a0-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="739a0-839">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="739a0-839">This sample doesn't use a template.</span></span> <span data-ttu-id="739a0-840">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="739a0-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="739a0-841">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="739a0-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="739a0-842">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="739a0-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="739a0-843">Valores de retorno 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-843">Return values 2.1</span></span>

<span data-ttu-id="739a0-844">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="739a0-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="739a0-845">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="739a0-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="739a0-846">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="739a0-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="739a0-847">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="739a0-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="739a0-848">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="739a0-849">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="739a0-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="739a0-850">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="739a0-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="739a0-851">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="739a0-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="739a0-852">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="739a0-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="739a0-853">Testar o método GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="739a0-854">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="739a0-855">Instalar o [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="739a0-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="739a0-856">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-856">Start the web app.</span></span>
* <span data-ttu-id="739a0-857">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-857">Start Postman.</span></span>
* <span data-ttu-id="739a0-858">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="739a0-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="739a0-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="739a0-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="739a0-860">Em **Arquivo** > **Configurações** (guia **Geral** ), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="739a0-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="739a0-861">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="739a0-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="739a0-862">De **Postman**  >  **preferências** do postmaster (guia **geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="739a0-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="739a0-863">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="739a0-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="739a0-864">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="739a0-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="739a0-865">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-865">Create a new request.</span></span>
  * <span data-ttu-id="739a0-866">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="739a0-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="739a0-867">Defina o URI de solicitação como `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="739a0-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="739a0-868">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="739a0-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="739a0-869">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="739a0-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="739a0-870">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-870">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="739a0-872">Adicionar um método Create 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-872">Add a Create method 2.1</span></span>

<span data-ttu-id="739a0-873">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="739a0-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="739a0-874">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="739a0-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="739a0-875">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="739a0-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="739a0-876">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="739a0-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="739a0-877">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="739a0-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="739a0-878">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="739a0-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="739a0-879">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="739a0-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="739a0-880">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="739a0-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="739a0-881">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="739a0-882">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="739a0-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="739a0-883">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="739a0-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="739a0-884">Testar o método PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="739a0-885">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-885">Build the project.</span></span>
* <span data-ttu-id="739a0-886">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="739a0-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="739a0-887">Defina o URI como `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="739a0-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="739a0-888">Por exemplo, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="739a0-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="739a0-889">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="739a0-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="739a0-890">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="739a0-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="739a0-891">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="739a0-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="739a0-892">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="739a0-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="739a0-893">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-893">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="739a0-895">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="739a0-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="739a0-896">Testar o URI do cabeçalho do local 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="739a0-897">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="739a0-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="739a0-898">Copie o valor do cabeçalho **Local** :</span><span class="sxs-lookup"><span data-stu-id="739a0-898">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="739a0-900">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="739a0-900">Set the method to GET.</span></span>
* <span data-ttu-id="739a0-901">Defina o URI como `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="739a0-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="739a0-902">Por exemplo, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="739a0-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="739a0-903">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="739a0-904">Adicionar um método PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="739a0-905">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="739a0-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="739a0-906">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="739a0-907">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="739a0-908">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="739a0-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="739a0-909">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="739a0-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="739a0-910">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="739a0-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="739a0-911">Testar o método PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="739a0-912">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="739a0-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="739a0-913">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="739a0-914">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="739a0-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="739a0-915">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="739a0-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="739a0-916">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="739a0-916">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="739a0-918">Adicionar um método DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="739a0-919">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="739a0-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="739a0-920">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="739a0-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="739a0-921">Testar o método DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="739a0-922">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="739a0-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="739a0-923">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="739a0-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="739a0-924">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="739a0-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="739a0-925">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="739a0-925">Select **Send**.</span></span>

<span data-ttu-id="739a0-926">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="739a0-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="739a0-927">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="739a0-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="739a0-928">Chamar a API Web com JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="739a0-929">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="739a0-930">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-930">jQuery initiates the request.</span></span> <span data-ttu-id="739a0-931">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="739a0-932">Configurar o aplicativo para [servir arquivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) e [habilitar o mapeamento de arquivo padrão](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="739a0-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="739a0-933">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="739a0-934">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="739a0-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="739a0-935">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="739a0-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="739a0-936">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="739a0-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="739a0-937">Substitua o conteúdo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="739a0-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="739a0-938">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="739a0-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="739a0-939">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="739a0-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="739a0-940">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="739a0-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="739a0-941">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="739a0-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="739a0-942">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="739a0-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="739a0-943">Obter uma lista de itens pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="739a0-944">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="739a0-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="739a0-945">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="739a0-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="739a0-946">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="739a0-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="739a0-947">Adicionar um item pendente 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="739a0-948">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="739a0-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="739a0-949">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="739a0-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="739a0-950">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="739a0-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="739a0-951">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="739a0-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="739a0-952">Atualizar um item de tarefas pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="739a0-953">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="739a0-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="739a0-954">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="739a0-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="739a0-955">Excluir um item de tarefas pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="739a0-956">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="739a0-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="739a0-957">Adicionar suporte de autenticação a uma API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="739a0-958">Recursos adicionais 2,1</span><span class="sxs-lookup"><span data-stu-id="739a0-958">Additional resources 2.1</span></span>

<span data-ttu-id="739a0-959">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="739a0-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="739a0-960">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="739a0-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="739a0-961">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="739a0-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="739a0-962">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="739a0-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
