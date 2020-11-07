---
title: 'Tutorial: introdução às :::no-loc(Razor)::: páginas no ASP.NET Core'
author: rick-anderson
description: 'Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um :::no-loc(Razor)::: aplicativo web ASP.NET Core Pages.'
ms.author: riande
ms.date: 09/15/2020
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360900"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="c3b71-103">Tutorial: introdução às :::no-loc(Razor)::: páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3b71-103">Tutorial: Get started with :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="c3b71-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c3b71-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="c3b71-105">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um :::no-loc(Razor)::: aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="c3b71-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

<span data-ttu-id="c3b71-106">Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às :::no-loc(Razor)::: páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="c3b71-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to :::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="c3b71-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="c3b71-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="c3b71-108">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c3b71-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c3b71-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="c3b71-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c3b71-110">:::no-loc(Create)::: um :::no-loc(Razor)::: aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-110">:::no-loc(Create)::: a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="c3b71-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-111">Run the app.</span></span>
> * <span data-ttu-id="c3b71-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-112">Examine the project files.</span></span>

<span data-ttu-id="c3b71-113">No final deste tutorial, você terá um :::no-loc(Razor)::: aplicativo Web de páginas de trabalho que será aprimorado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="c3b71-113">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll enhance in later tutorials.</span></span>

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="c3b71-115">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c3b71-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="c3b71-119">:::no-loc(Create)::: um :::no-loc(Razor)::: aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="c3b71-119">:::no-loc(Create)::: a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c3b71-121">Inicie o Visual Studio e selecione **:::no-loc(Create)::: um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-121">Start Visual Studio and select **:::no-loc(Create)::: a new project**.</span></span> <span data-ttu-id="c3b71-122">Para obter mais informações, consulte [ :::no-loc(Create)::: um novo projeto no Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="c3b71-122">For more information, see [:::no-loc(Create)::: a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![::: no-Loc (criar)::: um novo projeto da janela inicial](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="c3b71-124">Na caixa de diálogo **:::no-loc(Create)::: novo projeto** , selecione **ASP.NET Core aplicativo Web** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-124">In the **:::no-loc(Create)::: a new project** dialog, select **ASP.NET Core Web Application** , and then select **Next**.</span></span>

    ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="c3b71-126">Na caixa de diálogo **configurar seu novo projeto** , digite `:::no-loc(Razor):::PagesMovie` para **nome do projeto**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-126">In the **Configure your new project** dialog, enter `:::no-loc(Razor):::PagesMovie` for **Project name**.</span></span> <span data-ttu-id="c3b71-127">É importante nomear o projeto *:::no-loc(Razor)::: PagesMovie* , incluindo a correspondência de maiúsculas e minúsculas, de modo que os namespaces corresponderão quando você copiar e colar o código de exemplo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-127">It's important to name the project *:::no-loc(Razor):::PagesMovie* , including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="c3b71-128">Selecione **:::no-loc(Create):::**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-128">Select **:::no-loc(Create):::**.</span></span>

    ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="c3b71-130">Na caixa de diálogo **:::no-loc(Create)::: novo aplicativo Web ASP.NET Core** , selecione:</span><span class="sxs-lookup"><span data-stu-id="c3b71-130">In the **:::no-loc(Create)::: a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="c3b71-131">**.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="c3b71-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="c3b71-132">**Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-132">**Web Application**.</span></span>
    1. <span data-ttu-id="c3b71-133">**:::no-loc(Create):::**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-133">**:::no-loc(Create):::**.</span></span>

     ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="c3b71-135">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="c3b71-135">The following starter project is created:</span></span>

    ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c3b71-138">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c3b71-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="c3b71-139">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="c3b71-140">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c3b71-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o :::no-loc(Razor):::PagesMovie
   code -r :::no-loc(Razor):::PagesMovie
   ```

   * <span data-ttu-id="c3b71-141">O `dotnet new` comando cria um novo :::no-loc(Razor)::: projeto de páginas na pasta *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="c3b71-141">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
   * <span data-ttu-id="c3b71-142">O `code` comando abre a pasta *:::no-loc(Razor)::: PagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c3b71-142">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-143">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c3b71-144">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-144">Select **File** > **New Solution**.</span></span>

    ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="c3b71-146">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="c3b71-147">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="c3b71-149">Na caixa de diálogo **Configurar o novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="c3b71-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="c3b71-150">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="c3b71-151">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão mais recente do .NET 5. x.</span><span class="sxs-lookup"><span data-stu-id="c3b71-151">If presented an option to select a **Target Framework** , select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="c3b71-152">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-152">Select **Next**.</span></span>

1. <span data-ttu-id="c3b71-153">Nomeie o projeto *:::no-loc(Razor)::: PagesMovie* e selecione **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="c3b71-153">Name the project *:::no-loc(Razor):::PagesMovie* and select **:::no-loc(Create):::**.</span></span>

    ![nome do macOS o projeto](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c3b71-155">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c3b71-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="c3b71-156">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="c3b71-156">Examine the project files</span></span>

<span data-ttu-id="c3b71-157">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="c3b71-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c3b71-158">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="c3b71-158">Pages folder</span></span>

<span data-ttu-id="c3b71-159">Contém :::no-loc(Razor)::: páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="c3b71-159">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="c3b71-160">Cada :::no-loc(Razor)::: página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="c3b71-160">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="c3b71-161">Um arquivo *. cshtml* que tem marcação HTML com código C# usando a :::no-loc(Razor)::: sintaxe.</span><span class="sxs-lookup"><span data-stu-id="c3b71-161">A *.cshtml* file that has HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="c3b71-162">Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.</span><span class="sxs-lookup"><span data-stu-id="c3b71-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="c3b71-163">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="c3b71-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c3b71-164">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c3b71-165">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="c3b71-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c3b71-166">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c3b71-167">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="c3b71-167">wwwroot folder</span></span>

<span data-ttu-id="c3b71-168">Contém ativos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="c3b71-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c3b71-169">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### :::no-loc(appsettings.json):::

<span data-ttu-id="c3b71-170">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="c3b71-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="c3b71-171">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c3b71-172">Module.vb</span><span class="sxs-lookup"><span data-stu-id="c3b71-172">Program.cs</span></span>

<span data-ttu-id="c3b71-173">Contém o ponto de entrada para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-173">Contains the entry point for the app.</span></span> <span data-ttu-id="c3b71-174">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c3b71-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c3b71-175">Startup.cs</span></span>

<span data-ttu-id="c3b71-176">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="c3b71-177">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c3b71-178">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c3b71-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c3b71-179">Em seguida: adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="c3b71-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c3b71-180">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um :::no-loc(Razor)::: aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="c3b71-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

<span data-ttu-id="c3b71-181">Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às :::no-loc(Razor)::: páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="c3b71-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to :::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="c3b71-182">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="c3b71-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="c3b71-183">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c3b71-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c3b71-184">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="c3b71-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c3b71-185">:::no-loc(Create)::: um :::no-loc(Razor)::: aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-185">:::no-loc(Create)::: a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="c3b71-186">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-186">Run the app.</span></span>
> * <span data-ttu-id="c3b71-187">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-187">Examine the project files.</span></span>

<span data-ttu-id="c3b71-188">No final deste tutorial, você terá um :::no-loc(Razor)::: aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="c3b71-188">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="c3b71-190">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c3b71-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="c3b71-194">:::no-loc(Create)::: um :::no-loc(Razor)::: aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="c3b71-194">:::no-loc(Create)::: a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c3b71-196">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c3b71-197">:::no-loc(Create)::: um novo aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-197">:::no-loc(Create)::: a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="c3b71-198">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="c3b71-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="c3b71-199">Nomeie o projeto **:::no-loc(Razor)::: PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-199">Name the project **:::no-loc(Razor):::PagesMovie**.</span></span> <span data-ttu-id="c3b71-200">É importante nomear o projeto *:::no-loc(Razor)::: PagesMovie* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="c3b71-200">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="c3b71-201">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="c3b71-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="c3b71-202">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web** e, em seguida, selecione **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="c3b71-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **:::no-loc(Create):::**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="c3b71-204">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="c3b71-204">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c3b71-207">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c3b71-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c3b71-208">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c3b71-209">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c3b71-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="c3b71-210">O `dotnet new` comando cria um novo :::no-loc(Razor)::: projeto de páginas na pasta *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="c3b71-210">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="c3b71-211">O `code` comando abre a pasta *:::no-loc(Razor)::: PagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c3b71-211">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c3b71-212">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' :::no-loc(Razor)::: PagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="c3b71-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="c3b71-213">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="c3b71-213">Select **Yes**.</span></span>

  <span data-ttu-id="c3b71-214">Um diretório *.vscode* , contendo os arquivos *launch.json* e *tasks.json* , é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c3b71-216">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-216">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c3b71-218">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="c3b71-219">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c3b71-221">Na caixa de diálogo **Configurar o novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="c3b71-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="c3b71-222">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c3b71-223">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="c3b71-223">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="c3b71-224">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-224">Select **Next**.</span></span>

* <span data-ttu-id="c3b71-225">Nomeie o projeto **:::no-loc(Razor)::: PagesMovie** e, em seguida, selecione **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="c3b71-225">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **:::no-loc(Create):::**.</span></span>

  ![nome do macOS o projeto](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c3b71-227">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c3b71-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="c3b71-228">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="c3b71-228">Examine the project files</span></span>

<span data-ttu-id="c3b71-229">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="c3b71-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c3b71-230">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="c3b71-230">Pages folder</span></span>

<span data-ttu-id="c3b71-231">Contém :::no-loc(Razor)::: páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="c3b71-231">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="c3b71-232">Cada :::no-loc(Razor)::: página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="c3b71-232">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="c3b71-233">Um arquivo *. cshtml* que tem marcação HTML com código C# usando a :::no-loc(Razor)::: sintaxe.</span><span class="sxs-lookup"><span data-stu-id="c3b71-233">A *.cshtml* file that has HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="c3b71-234">Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.</span><span class="sxs-lookup"><span data-stu-id="c3b71-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="c3b71-235">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="c3b71-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c3b71-236">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c3b71-237">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="c3b71-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c3b71-238">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c3b71-239">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="c3b71-239">wwwroot folder</span></span>

<span data-ttu-id="c3b71-240">Contém arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="c3b71-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c3b71-241">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c3b71-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="c3b71-242">appSettings.json</span></span>

<span data-ttu-id="c3b71-243">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="c3b71-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="c3b71-244">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c3b71-245">Module.vb</span><span class="sxs-lookup"><span data-stu-id="c3b71-245">Program.cs</span></span>

<span data-ttu-id="c3b71-246">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="c3b71-246">Contains the entry point for the program.</span></span> <span data-ttu-id="c3b71-247">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c3b71-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c3b71-248">Startup.cs</span></span>

<span data-ttu-id="c3b71-249">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="c3b71-250">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c3b71-251">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c3b71-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c3b71-252">Em seguida: adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="c3b71-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3b71-253">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="c3b71-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="c3b71-254">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um :::no-loc(Razor)::: aplicativo Web de ASP.NET Core páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

<span data-ttu-id="c3b71-255">Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às :::no-loc(Razor)::: páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="c3b71-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to :::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="c3b71-256">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="c3b71-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="c3b71-257">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c3b71-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c3b71-258">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="c3b71-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c3b71-259">:::no-loc(Create)::: um :::no-loc(Razor)::: aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-259">:::no-loc(Create)::: a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="c3b71-260">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-260">Run the app.</span></span>
> * <span data-ttu-id="c3b71-261">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-261">Examine the project files.</span></span>

<span data-ttu-id="c3b71-262">No final deste tutorial, você terá um :::no-loc(Razor)::: aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="c3b71-262">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="c3b71-264">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c3b71-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-267">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="c3b71-268">:::no-loc(Create)::: um :::no-loc(Razor)::: aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="c3b71-268">:::no-loc(Create)::: a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c3b71-270">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="c3b71-271">:::no-loc(Create)::: um novo aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-271">:::no-loc(Create)::: a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="c3b71-273">Nomeie o projeto **:::no-loc(Razor)::: PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-273">Name the project **:::no-loc(Razor):::PagesMovie**.</span></span> <span data-ttu-id="c3b71-274">É importante nomear o projeto *:::no-loc(Razor)::: PagesMovie* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="c3b71-274">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="c3b71-276">Selecione **ASP.NET Core 2,2** no menu suspenso, **aplicativo Web** e, em seguida, selecione **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="c3b71-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **:::no-loc(Create):::**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="c3b71-278">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="c3b71-278">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c3b71-281">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c3b71-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c3b71-282">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c3b71-283">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c3b71-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="c3b71-284">O `dotnet new` comando cria um novo :::no-loc(Razor)::: projeto de páginas na pasta *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="c3b71-284">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="c3b71-285">O `code` comando abre a pasta *:::no-loc(Razor)::: PagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c3b71-285">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c3b71-286">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' :::no-loc(Razor)::: PagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="c3b71-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="c3b71-287">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="c3b71-287">Select **Yes**.</span></span>

  <span data-ttu-id="c3b71-288">Um diretório *.vscode* , contendo os arquivos *launch.json* e *tasks.json* , é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="c3b71-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-289">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c3b71-290">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-290">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c3b71-292">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="c3b71-293">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="c3b71-294">Na caixa de diálogo **Configurar o novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="c3b71-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="c3b71-295">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c3b71-296">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="c3b71-296">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="c3b71-297">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c3b71-297">Select **Next**.</span></span>

* <span data-ttu-id="c3b71-298">Nomeie o projeto **:::no-loc(Razor)::: PagesMovie** e, em seguida, selecione **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="c3b71-298">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **:::no-loc(Create):::**.</span></span>

  ![nameproj](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c3b71-300">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c3b71-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3b71-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3b71-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c3b71-302">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="c3b71-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="c3b71-303">Iniciar o aplicativo com <kbd>Ctrl + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="c3b71-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c3b71-304">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="c3b71-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>


  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c3b71-305">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3b71-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c3b71-306">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c3b71-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c3b71-307">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="c3b71-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c3b71-308">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="c3b71-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c3b71-309">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="c3b71-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="c3b71-310">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="c3b71-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c3b71-311">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="c3b71-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c3b71-313">A imagem a seguir mostra o aplicativo após o consentimento para acompanhamento é fornecido:</span><span class="sxs-lookup"><span data-stu-id="c3b71-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="c3b71-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3b71-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="c3b71-316">Pressione <kbd>Ctrl + F5</kbd> para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="c3b71-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="c3b71-317">Iniciar o aplicativo com <kbd>Ctrl + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="c3b71-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c3b71-318">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="c3b71-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="c3b71-319">Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e vai para `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c3b71-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="c3b71-320">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c3b71-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c3b71-321">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="c3b71-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c3b71-322">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="c3b71-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="c3b71-323">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="c3b71-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c3b71-324">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="c3b71-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c3b71-326">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="c3b71-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3b71-328">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c3b71-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c3b71-329">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="c3b71-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c3b71-330">Iniciar o aplicativo com <kbd>cmd + opt + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="c3b71-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c3b71-331">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="c3b71-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="c3b71-332">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e vai para `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c3b71-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="c3b71-333">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="c3b71-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c3b71-334">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="c3b71-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="c3b71-336">A imagem a seguir mostra o aplicativo após o consentimento para acompanhamento é fornecido:</span><span class="sxs-lookup"><span data-stu-id="c3b71-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="c3b71-338">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="c3b71-338">Examine the project files</span></span>

<span data-ttu-id="c3b71-339">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="c3b71-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c3b71-340">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="c3b71-340">Pages folder</span></span>

<span data-ttu-id="c3b71-341">Contém :::no-loc(Razor)::: páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="c3b71-341">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="c3b71-342">Cada :::no-loc(Razor)::: página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="c3b71-342">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="c3b71-343">Um arquivo *. cshtml* que tem marcação HTML com código C# usando a :::no-loc(Razor)::: sintaxe.</span><span class="sxs-lookup"><span data-stu-id="c3b71-343">A *.cshtml* file that has HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="c3b71-344">Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.</span><span class="sxs-lookup"><span data-stu-id="c3b71-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="c3b71-345">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="c3b71-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c3b71-346">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="c3b71-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c3b71-347">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="c3b71-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c3b71-348">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="c3b71-349">:::no-loc(Razor)::: As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="c3b71-349">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="c3b71-350">Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="c3b71-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c3b71-351">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="c3b71-351">wwwroot folder</span></span>

<span data-ttu-id="c3b71-352">Contém arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="c3b71-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c3b71-353">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c3b71-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="c3b71-354">appSettings.json</span></span>

<span data-ttu-id="c3b71-355">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="c3b71-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="c3b71-356">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c3b71-357">Module.vb</span><span class="sxs-lookup"><span data-stu-id="c3b71-357">Program.cs</span></span>

<span data-ttu-id="c3b71-358">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="c3b71-358">Contains the entry point for the program.</span></span> <span data-ttu-id="c3b71-359">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c3b71-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c3b71-360">Startup.cs</span></span>

<span data-ttu-id="c3b71-361">Contém o código que configura o comportamento do aplicativo, como se ele requer consentimento para :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="c3b71-361">Contains code that configures app behavior, such as whether it requires consent for :::no-loc(cookie):::s.</span></span> <span data-ttu-id="c3b71-362">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c3b71-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3b71-363">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c3b71-363">Additional resources</span></span>

* [<span data-ttu-id="c3b71-364">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="c3b71-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="c3b71-365">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c3b71-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c3b71-366">Em seguida: adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="c3b71-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
