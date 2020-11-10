---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422768"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="cebd0-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="cebd0-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="cebd0-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cebd0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="cebd0-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cebd0-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="cebd0-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="cebd0-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="cebd0-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="cebd0-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cebd0-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-108">Create a web app.</span></span>
> * <span data-ttu-id="cebd0-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="cebd0-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cebd0-110">Work with a database.</span></span>
> * <span data-ttu-id="cebd0-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="cebd0-111">Add search and validation.</span></span>

<span data-ttu-id="cebd0-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="cebd0-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="cebd0-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cebd0-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="cebd0-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="cebd0-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cebd0-119">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="cebd0-120">Na caixa de diálogo **criar um novo projeto** , selecione **ASP.NET Core aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="cebd0-121">Na caixa de diálogo **configurar seu novo projeto** , digite `MvcMovie` para **nome do projeto**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="cebd0-122">É importante usar esse nome exato, incluindo a capitalização, para que cada `namespace` correspondência seja feita quando o código for copiado.</span><span class="sxs-lookup"><span data-stu-id="cebd0-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="cebd0-123">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-123">Select **Create**.</span></span>
1. <span data-ttu-id="cebd0-124">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:</span><span class="sxs-lookup"><span data-stu-id="cebd0-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="cebd0-125">**.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="cebd0-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="cebd0-126">**ASP.NET Core aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="cebd0-127">**Criar**</span><span class="sxs-lookup"><span data-stu-id="cebd0-127">**Create**</span></span>

![<span data-ttu-id="cebd0-128">Criar um novo aplicativo Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cebd0-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/5/mvc.png)

<span data-ttu-id="cebd0-129">Para obter abordagens alternativas para criar o projeto, consulte [criar um novo projeto no Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="cebd0-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="cebd0-130">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="cebd0-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="cebd0-131">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="cebd0-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="cebd0-132">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="cebd0-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cebd0-134">O tutorial pressupõe familiaridade com VS Code.</span><span class="sxs-lookup"><span data-stu-id="cebd0-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="cebd0-135">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="cebd0-136">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cebd0-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cebd0-137">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="cebd0-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="cebd0-138">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cebd0-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="cebd0-139">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="cebd0-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="cebd0-140">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="cebd0-140">Select **Yes**</span></span>

  * <span data-ttu-id="cebd0-141">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="cebd0-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="cebd0-142">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cebd0-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-143">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cebd0-144">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-144">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cebd0-146">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="cebd0-147">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="cebd0-149">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="cebd0-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="cebd0-150">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="cebd0-151">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 5. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-151">If presented an option to select a **Target Framework** , select the latest 5.x version.</span></span>

  <span data-ttu-id="cebd0-152">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-152">Select **Next**.</span></span>

* <span data-ttu-id="cebd0-153">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-153">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="cebd0-155">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cebd0-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cebd0-157">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="cebd0-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="cebd0-158">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="cebd0-159">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-160">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="cebd0-161">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="cebd0-162">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cebd0-163">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="cebd0-164">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="cebd0-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="cebd0-166">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cebd0-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="cebd0-168">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cebd0-168">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cebd0-171">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="cebd0-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="cebd0-172">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="cebd0-173">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-174">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="cebd0-175">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="cebd0-176">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cebd0-177">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cebd0-180">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="cebd0-181">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="cebd0-182">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-183">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="cebd0-184">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="cebd0-185">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="cebd0-186">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="cebd0-187">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cebd0-187">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="cebd0-189">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cebd0-190">Próximo</span><span class="sxs-lookup"><span data-stu-id="cebd0-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="cebd0-191">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cebd0-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="cebd0-192">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="cebd0-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="cebd0-193">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="cebd0-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cebd0-194">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-194">Create a web app.</span></span>
> * <span data-ttu-id="cebd0-195">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="cebd0-196">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cebd0-196">Work with a database.</span></span>
> * <span data-ttu-id="cebd0-197">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="cebd0-197">Add search and validation.</span></span>

<span data-ttu-id="cebd0-198">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="cebd0-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="cebd0-199">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cebd0-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="cebd0-203">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="cebd0-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cebd0-205">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="cebd0-206">Selecione **ASP.NET Core aplicativo Web** a > **seguir**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="cebd0-208">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="cebd0-209">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="cebd0-211">Selecione **aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="cebd0-212">Nas caixas suspensas, selecione **.NET Core** e **ASP.NET Core 3,1** e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="cebd0-213">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cebd0-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="cebd0-214">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="cebd0-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="cebd0-215">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="cebd0-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="cebd0-216">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="cebd0-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cebd0-218">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="cebd0-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="cebd0-219">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="cebd0-220">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cebd0-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cebd0-221">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="cebd0-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="cebd0-222">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cebd0-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="cebd0-223">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="cebd0-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="cebd0-224">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="cebd0-224">Select **Yes**</span></span>

  * <span data-ttu-id="cebd0-225">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="cebd0-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="cebd0-226">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cebd0-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-227">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cebd0-228">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-228">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cebd0-230">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="cebd0-231">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="cebd0-233">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="cebd0-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="cebd0-234">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="cebd0-235">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-235">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="cebd0-236">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-236">Select **Next**.</span></span>

* <span data-ttu-id="cebd0-237">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-237">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="cebd0-239">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cebd0-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cebd0-241">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="cebd0-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="cebd0-242">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="cebd0-243">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-244">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="cebd0-245">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="cebd0-246">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cebd0-247">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="cebd0-248">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="cebd0-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="cebd0-250">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cebd0-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="cebd0-252">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cebd0-252">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cebd0-255">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="cebd0-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="cebd0-256">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="cebd0-257">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-258">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="cebd0-259">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="cebd0-260">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cebd0-261">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-263">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cebd0-264">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="cebd0-265">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="cebd0-266">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-267">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="cebd0-268">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="cebd0-269">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="cebd0-270">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="cebd0-271">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cebd0-271">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="cebd0-273">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cebd0-274">Próximo</span><span class="sxs-lookup"><span data-stu-id="cebd0-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="cebd0-275">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cebd0-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="cebd0-276">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="cebd0-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="cebd0-277">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="cebd0-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cebd0-278">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-278">Create a web app.</span></span>
> * <span data-ttu-id="cebd0-279">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="cebd0-280">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cebd0-280">Work with a database.</span></span>
> * <span data-ttu-id="cebd0-281">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="cebd0-281">Add search and validation.</span></span>

<span data-ttu-id="cebd0-282">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="cebd0-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="cebd0-283">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cebd0-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-286">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="cebd0-287">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="cebd0-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cebd0-289">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="cebd0-290">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="cebd0-292">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="cebd0-293">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="cebd0-295">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-295">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="cebd0-296">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cebd0-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="cebd0-297">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="cebd0-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="cebd0-298">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="cebd0-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="cebd0-299">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="cebd0-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cebd0-301">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="cebd0-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="cebd0-302">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="cebd0-303">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cebd0-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cebd0-304">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="cebd0-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="cebd0-305">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cebd0-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="cebd0-306">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="cebd0-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="cebd0-307">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="cebd0-307">Select **Yes**</span></span>

  * <span data-ttu-id="cebd0-308">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="cebd0-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="cebd0-309">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cebd0-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-310">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cebd0-311">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-311">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cebd0-313">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="cebd0-314">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="cebd0-315">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="cebd0-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="cebd0-316">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="cebd0-317">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-317">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="cebd0-318">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-318">Select **Next**.</span></span>

* <span data-ttu-id="cebd0-319">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-319">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="cebd0-320">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cebd0-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cebd0-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cebd0-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cebd0-322">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="cebd0-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="cebd0-323">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="cebd0-324">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-325">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="cebd0-326">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="cebd0-327">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cebd0-328">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="cebd0-329">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="cebd0-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="cebd0-331">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cebd0-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="cebd0-333">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="cebd0-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="cebd0-334">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="cebd0-334">This app doesn't track personal information.</span></span> <span data-ttu-id="cebd0-335">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cebd0-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="cebd0-337">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="cebd0-337">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cebd0-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cebd0-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cebd0-340">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="cebd0-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="cebd0-341">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="cebd0-342">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-343">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="cebd0-344">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="cebd0-345">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cebd0-346">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="cebd0-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="cebd0-347">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="cebd0-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="cebd0-348">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="cebd0-348">This app doesn't track personal information.</span></span> <span data-ttu-id="cebd0-349">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cebd0-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="cebd0-351">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="cebd0-351">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cebd0-353">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cebd0-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cebd0-354">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cebd0-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="cebd0-355">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="cebd0-356">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cebd0-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cebd0-357">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="cebd0-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="cebd0-358">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cebd0-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="cebd0-359">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="cebd0-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="cebd0-360">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="cebd0-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="cebd0-361">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="cebd0-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="cebd0-362">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="cebd0-362">This app doesn't track personal information.</span></span> <span data-ttu-id="cebd0-363">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cebd0-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="cebd0-365">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="cebd0-365">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="cebd0-367">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="cebd0-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cebd0-368">Próximo</span><span class="sxs-lookup"><span data-stu-id="cebd0-368">Next</span></span>](adding-controller.md)

::: moniker-end
