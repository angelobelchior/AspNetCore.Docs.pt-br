---
title: 'Tutorial: introdução às Razor páginas no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor páginas no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor páginas, usar Entity Framework Core e SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021088"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="77f52-104">Tutorial: introdução às Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77f52-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="77f52-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77f52-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="77f52-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="77f52-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="77f52-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="77f52-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="77f52-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="77f52-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77f52-109">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="77f52-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="77f52-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77f52-110">Run the app.</span></span>
> * <span data-ttu-id="77f52-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="77f52-111">Examine the project files.</span></span>

<span data-ttu-id="77f52-112">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="77f52-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="77f52-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="77f52-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77f52-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77f52-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="77f52-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77f52-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77f52-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="77f52-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="77f52-118">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="77f52-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77f52-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77f52-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77f52-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="77f52-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="77f52-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="77f52-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="77f52-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="77f52-123">Nomeie o projeto \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="77f52-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="77f52-124">É importante nomear o projeto \* Razor PagesMovie\* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="77f52-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="77f52-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="77f52-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="77f52-126">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="77f52-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="77f52-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="77f52-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77f52-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77f52-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="77f52-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="77f52-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="77f52-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="77f52-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="77f52-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="77f52-134">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="77f52-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="77f52-135">O `code` comando abre a pasta \* Razor PagesMovie\* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="77f52-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="77f52-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' Razor PagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="77f52-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="77f52-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="77f52-137">Select **Yes**.</span></span>

  <span data-ttu-id="77f52-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="77f52-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77f52-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="77f52-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="77f52-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="77f52-140">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="77f52-142">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="77f52-143">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="77f52-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="77f52-145">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="77f52-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="77f52-146">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="77f52-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="77f52-147">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="77f52-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="77f52-148">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-148">Select **Next**.</span></span>

* <span data-ttu-id="77f52-149">Nomeie o projeto \*\* Razor PagesMovie\*\*e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="77f52-151">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="77f52-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="77f52-152">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="77f52-152">Examine the project files</span></span>

<span data-ttu-id="77f52-153">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="77f52-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="77f52-154">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="77f52-154">Pages folder</span></span>

<span data-ttu-id="77f52-155">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="77f52-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="77f52-156">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="77f52-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="77f52-157">Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="77f52-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="77f52-158">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="77f52-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="77f52-159">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="77f52-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="77f52-160">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="77f52-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="77f52-161">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="77f52-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="77f52-162">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="77f52-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="77f52-163">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="77f52-163">wwwroot folder</span></span>

<span data-ttu-id="77f52-164">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="77f52-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="77f52-165">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="77f52-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="77f52-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="77f52-166">appSettings.json</span></span>

<span data-ttu-id="77f52-167">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="77f52-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="77f52-168">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="77f52-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="77f52-169">Module.vb</span><span class="sxs-lookup"><span data-stu-id="77f52-169">Program.cs</span></span>

<span data-ttu-id="77f52-170">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="77f52-170">Contains the entry point for the program.</span></span> <span data-ttu-id="77f52-171">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="77f52-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="77f52-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="77f52-172">Startup.cs</span></span>

<span data-ttu-id="77f52-173">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77f52-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="77f52-174">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="77f52-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="77f52-175">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="77f52-175">Next steps</span></span>

<span data-ttu-id="77f52-176">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="77f52-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="77f52-177">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="77f52-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77f52-178">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="77f52-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="77f52-179">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um Razor aplicativo Web de ASP.NET Core páginas.</span><span class="sxs-lookup"><span data-stu-id="77f52-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="77f52-180">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="77f52-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="77f52-181">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="77f52-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77f52-182">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="77f52-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="77f52-183">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77f52-183">Run the app.</span></span>
> * <span data-ttu-id="77f52-184">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="77f52-184">Examine the project files.</span></span>

<span data-ttu-id="77f52-185">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="77f52-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="77f52-187">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="77f52-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77f52-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77f52-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="77f52-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77f52-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77f52-190">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="77f52-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="77f52-191">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="77f52-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77f52-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77f52-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77f52-193">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="77f52-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="77f52-194">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="77f52-196">Nomeie o projeto \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="77f52-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="77f52-197">É importante nomear o projeto \* Razor PagesMovie\* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="77f52-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="77f52-199">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="77f52-201">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="77f52-201">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="77f52-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77f52-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77f52-204">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="77f52-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="77f52-205">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="77f52-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="77f52-206">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="77f52-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="77f52-207">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="77f52-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="77f52-208">O `code` comando abre a pasta \* Razor PagesMovie\* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="77f52-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="77f52-209">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' Razor PagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="77f52-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="77f52-210">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="77f52-210">Select **Yes**.</span></span>

  <span data-ttu-id="77f52-211">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="77f52-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77f52-212">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="77f52-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="77f52-213">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="77f52-213">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="77f52-215">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="77f52-216">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="77f52-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="77f52-217">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="77f52-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="77f52-218">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="77f52-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="77f52-219">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="77f52-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="77f52-220">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-220">Select **Next**.</span></span>

* <span data-ttu-id="77f52-221">Nomeie o projeto \*\* Razor PagesMovie\*\*e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="77f52-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="77f52-223">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="77f52-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77f52-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77f52-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77f52-225">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="77f52-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="77f52-226">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77f52-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="77f52-227">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="77f52-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="77f52-228">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="77f52-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="77f52-229">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="77f52-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="77f52-230">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="77f52-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="77f52-231">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="77f52-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="77f52-232">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="77f52-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="77f52-234">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="77f52-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="77f52-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77f52-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="77f52-237">Pressione **Ctrl-F5** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="77f52-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="77f52-238">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="77f52-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="77f52-239">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="77f52-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="77f52-240">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="77f52-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="77f52-241">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="77f52-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="77f52-242">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="77f52-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="77f52-243">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="77f52-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="77f52-245">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="77f52-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77f52-247">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="77f52-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="77f52-248">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="77f52-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="77f52-249">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="77f52-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="77f52-250">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="77f52-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="77f52-251">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="77f52-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="77f52-253">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="77f52-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="77f52-255">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="77f52-255">Examine the project files</span></span>

<span data-ttu-id="77f52-256">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="77f52-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="77f52-257">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="77f52-257">Pages folder</span></span>

<span data-ttu-id="77f52-258">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="77f52-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="77f52-259">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="77f52-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="77f52-260">Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="77f52-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="77f52-261">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="77f52-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="77f52-262">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="77f52-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="77f52-263">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="77f52-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="77f52-264">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="77f52-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="77f52-265">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="77f52-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="77f52-266">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="77f52-266">wwwroot folder</span></span>

<span data-ttu-id="77f52-267">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="77f52-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="77f52-268">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="77f52-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="77f52-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="77f52-269">appSettings.json</span></span>

<span data-ttu-id="77f52-270">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="77f52-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="77f52-271">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="77f52-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="77f52-272">Module.vb</span><span class="sxs-lookup"><span data-stu-id="77f52-272">Program.cs</span></span>

<span data-ttu-id="77f52-273">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="77f52-273">Contains the entry point for the program.</span></span> <span data-ttu-id="77f52-274">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="77f52-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="77f52-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="77f52-275">Startup.cs</span></span>

<span data-ttu-id="77f52-276">Contém o código que configura o comportamento do aplicativo, como se ele requer consentimento para cookie s.</span><span class="sxs-lookup"><span data-stu-id="77f52-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="77f52-277">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="77f52-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77f52-278">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="77f52-278">Additional resources</span></span>

* [<span data-ttu-id="77f52-279">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="77f52-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="77f52-280">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="77f52-280">Next steps</span></span>

<span data-ttu-id="77f52-281">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="77f52-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="77f52-282">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="77f52-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
