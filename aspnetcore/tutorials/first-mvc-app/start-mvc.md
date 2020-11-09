---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cf17aaf8eff342c378536d4f635e09b936459bee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052897"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="8eaec-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8eaec-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="8eaec-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8eaec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8eaec-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8eaec-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="8eaec-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="8eaec-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="8eaec-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="8eaec-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8eaec-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="8eaec-108">Create a web app.</span></span>
> * <span data-ttu-id="8eaec-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="8eaec-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8eaec-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8eaec-110">Work with a database.</span></span>
> * <span data-ttu-id="8eaec-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="8eaec-111">Add search and validation.</span></span>

<span data-ttu-id="8eaec-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="8eaec-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="8eaec-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="8eaec-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8eaec-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8eaec-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8eaec-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8eaec-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8eaec-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8eaec-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="8eaec-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8eaec-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8eaec-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8eaec-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8eaec-119">No Visual Studio, selecione **Criar um projeto** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-119">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="8eaec-120">Selecione **ASP.NET Core aplicativo Web** a > **seguir** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-120">Select **ASP.NET Core Web Application** > **Next** .</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8eaec-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-122">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="8eaec-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="8eaec-125">Selecione **aplicativo Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-125">Select **Web Application(Model-View-Controller)** .</span></span> <span data-ttu-id="8eaec-126">Nas caixas suspensas, selecione **.NET Core** e **ASP.NET Core 3,1** e, em seguida, selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create** .</span></span>

![<span data-ttu-id="8eaec-127">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8eaec-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="8eaec-128">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="8eaec-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="8eaec-129">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="8eaec-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8eaec-130">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="8eaec-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8eaec-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8eaec-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8eaec-132">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="8eaec-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="8eaec-133">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="8eaec-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="8eaec-134">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8eaec-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8eaec-135">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="8eaec-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8eaec-136">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8eaec-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8eaec-137">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="8eaec-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="8eaec-138">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="8eaec-138">Select **Yes**</span></span>

  * <span data-ttu-id="8eaec-139">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="8eaec-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8eaec-140">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8eaec-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8eaec-141">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8eaec-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8eaec-142">Selecione **Arquivo** > **Nova Solução** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-142">Select **File** > **New Solution** .</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8eaec-144">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="8eaec-145">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="8eaec-147">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="8eaec-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="8eaec-148">Confirme se a **autenticação** está definida como **sem autenticação** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-148">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="8eaec-149">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="8eaec-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="8eaec-150">Selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-150">Select **Next** .</span></span>

* <span data-ttu-id="8eaec-151">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-151">Name the project **MvcMovie** , and then select **Create** .</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="8eaec-153">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="8eaec-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8eaec-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8eaec-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8eaec-155">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="8eaec-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="8eaec-156">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8eaec-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8eaec-157">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8eaec-158">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8eaec-159">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="8eaec-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="8eaec-160">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8eaec-161">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="8eaec-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8eaec-162">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="8eaec-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8eaec-164">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8eaec-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="8eaec-166">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8eaec-166">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8eaec-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8eaec-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8eaec-169">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="8eaec-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8eaec-170">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="8eaec-171">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8eaec-172">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8eaec-173">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="8eaec-174">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8eaec-175">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="8eaec-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8eaec-177">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8eaec-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8eaec-178">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8eaec-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="8eaec-179">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="8eaec-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8eaec-180">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8eaec-181">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8eaec-182">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="8eaec-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8eaec-183">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="8eaec-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8eaec-184">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="8eaec-185">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8eaec-185">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8eaec-187">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8eaec-188">Próximo</span><span class="sxs-lookup"><span data-stu-id="8eaec-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8eaec-189">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8eaec-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="8eaec-190">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="8eaec-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="8eaec-191">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="8eaec-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8eaec-192">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="8eaec-192">Create a web app.</span></span>
> * <span data-ttu-id="8eaec-193">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="8eaec-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8eaec-194">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8eaec-194">Work with a database.</span></span>
> * <span data-ttu-id="8eaec-195">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="8eaec-195">Add search and validation.</span></span>

<span data-ttu-id="8eaec-196">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="8eaec-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="8eaec-197">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="8eaec-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8eaec-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8eaec-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8eaec-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8eaec-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8eaec-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8eaec-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="8eaec-201">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8eaec-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8eaec-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8eaec-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8eaec-203">No Visual Studio, selecione **Criar um projeto** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-203">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="8eaec-204">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-204">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8eaec-206">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-206">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="8eaec-207">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="8eaec-209">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-209">Select **Web Application(Model-View-Controller)** , and then select **Create** .</span></span>

![<span data-ttu-id="8eaec-210">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8eaec-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="8eaec-211">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="8eaec-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="8eaec-212">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="8eaec-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8eaec-213">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="8eaec-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8eaec-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8eaec-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8eaec-215">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="8eaec-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="8eaec-216">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="8eaec-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="8eaec-217">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8eaec-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8eaec-218">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="8eaec-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8eaec-219">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8eaec-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8eaec-220">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="8eaec-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="8eaec-221">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="8eaec-221">Select **Yes**</span></span>

  * <span data-ttu-id="8eaec-222">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="8eaec-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8eaec-223">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8eaec-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8eaec-224">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8eaec-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8eaec-225">Selecione **Arquivo** > **Nova Solução** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-225">Select **File** > **New Solution** .</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8eaec-227">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="8eaec-228">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

* <span data-ttu-id="8eaec-229">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="8eaec-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="8eaec-230">Confirme se a **autenticação** está definida como **sem autenticação** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-230">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="8eaec-231">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="8eaec-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="8eaec-232">Selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-232">Select **Next** .</span></span>

* <span data-ttu-id="8eaec-233">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-233">Name the project **MvcMovie** , and then select **Create** .</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="8eaec-234">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="8eaec-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8eaec-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8eaec-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8eaec-236">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="8eaec-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="8eaec-237">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8eaec-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8eaec-238">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8eaec-239">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8eaec-240">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="8eaec-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="8eaec-241">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8eaec-242">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="8eaec-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8eaec-243">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="8eaec-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8eaec-245">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8eaec-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="8eaec-247">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="8eaec-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8eaec-248">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="8eaec-248">This app doesn't track personal information.</span></span> <span data-ttu-id="8eaec-249">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8eaec-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="8eaec-251">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="8eaec-251">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8eaec-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8eaec-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8eaec-254">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="8eaec-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8eaec-255">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="8eaec-256">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8eaec-257">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8eaec-258">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="8eaec-259">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8eaec-260">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="8eaec-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="8eaec-261">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="8eaec-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8eaec-262">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="8eaec-262">This app doesn't track personal information.</span></span> <span data-ttu-id="8eaec-263">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8eaec-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="8eaec-265">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="8eaec-265">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8eaec-267">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8eaec-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8eaec-268">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8eaec-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="8eaec-269">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="8eaec-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8eaec-270">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8eaec-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8eaec-271">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8eaec-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8eaec-272">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="8eaec-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8eaec-273">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="8eaec-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8eaec-274">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar** .</span><span class="sxs-lookup"><span data-stu-id="8eaec-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="8eaec-275">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="8eaec-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8eaec-276">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="8eaec-276">This app doesn't track personal information.</span></span> <span data-ttu-id="8eaec-277">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8eaec-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="8eaec-279">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="8eaec-279">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8eaec-281">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="8eaec-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8eaec-282">Próximo</span><span class="sxs-lookup"><span data-stu-id="8eaec-282">Next</span></span>](adding-controller.md)

::: moniker-end
