---
title: 'Usar ASP.NET Core :::no-loc(SignalR)::: com :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Crie um aplicativo de chat que usa ASP.NET Core :::no-loc(SignalR)::: com :::no-loc(Blazor WebAssembly)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: afbfc44db8cb833df7a79061f9bd73052859fec2
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508169"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="963c0-103">Usar ASP.NET Core :::no-loc(SignalR)::: com :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="963c0-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="963c0-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="963c0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="963c0-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o :::no-loc(SignalR)::: com o :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="963c0-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="963c0-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="963c0-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="963c0-107">Criar um :::no-loc(Blazor WebAssembly)::: projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="963c0-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="963c0-108">Adicionar a :::no-loc(SignalR)::: biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="963c0-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="963c0-109">Adicionar um :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="963c0-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="963c0-110">Adicionar :::no-loc(SignalR)::: serviços e um ponto de extremidade para o :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="963c0-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="963c0-111">Adicionar :::no-loc(Razor)::: código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="963c0-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="963c0-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="963c0-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="963c0-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="963c0-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="963c0-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="963c0-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="963c0-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="963c0-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="963c0-116">[Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="963c0-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="963c0-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="963c0-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="963c0-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="963c0-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="963c0-119">Visual Studio para Mac versão 8,8 ou posterior</span><span class="sxs-lookup"><span data-stu-id="963c0-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="963c0-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="963c0-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="963c0-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="963c0-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="963c0-122">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="963c0-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="963c0-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="963c0-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="963c0-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="963c0-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="963c0-125">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="963c0-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="963c0-126">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="963c0-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="963c0-127">Criar um :::no-loc(Blazor WebAssembly)::: projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="963c0-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="963c0-128">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="963c0-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="963c0-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="963c0-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="963c0-130">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="963c0-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="963c0-131">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="963c0-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="963c0-132">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="963c0-132">Create a new project.</span></span>

1. <span data-ttu-id="963c0-133">Selecione **:::no-loc(Blazor)::: aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="963c0-134">Digite `:::no-loc(Blazor)::::::no-loc(SignalR):::App` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="963c0-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="963c0-135">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="963c0-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="963c0-136">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-136">Select **Create**.</span></span>

1. <span data-ttu-id="963c0-137">Escolha o modelo de **:::no-loc(Blazor WebAssembly)::: aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="963c0-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="963c0-138">Em **avançado** , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="963c0-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="963c0-139">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="963c0-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="963c0-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="963c0-141">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="963c0-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="963c0-142">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="963c0-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="963c0-143">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="963c0-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="963c0-144">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="963c0-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="963c0-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="963c0-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="963c0-146">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="963c0-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="963c0-147">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="963c0-148">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="963c0-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="963c0-149">Escolha o modelo de **:::no-loc(Blazor WebAssembly)::: aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="963c0-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="963c0-150">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-150">Select **Next**.</span></span>

1. <span data-ttu-id="963c0-151">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="963c0-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="963c0-152">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="963c0-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="963c0-153">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-153">Select **Next**.</span></span>

1. <span data-ttu-id="963c0-154">No campo **nome do projeto** , nomeie o aplicativo `:::no-loc(Blazor)::::::no-loc(SignalR):::App` .</span><span class="sxs-lookup"><span data-stu-id="963c0-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="963c0-155">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-155">Select **Create**.</span></span>

   <span data-ttu-id="963c0-156">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="963c0-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="963c0-157">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="963c0-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="963c0-158">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="963c0-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="963c0-159">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="963c0-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="963c0-160">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="963c0-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="963c0-161">Adicionar a :::no-loc(SignalR)::: biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="963c0-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="963c0-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="963c0-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="963c0-163">Em **Gerenciador de soluções** , clique com o botão direito do mouse no `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="963c0-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="963c0-164">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="963c0-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="963c0-165">Com **procurar** selecionado, digite `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="963c0-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="963c0-166">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="963c0-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="963c0-167">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="963c0-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="963c0-168">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="963c0-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="963c0-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="963c0-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="963c0-170">No **terminal integrado** ( **Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="963c0-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="963c0-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="963c0-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="963c0-172">Na barra lateral da **solução** , clique com o botão direito do mouse no `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="963c0-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="963c0-173">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="963c0-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="963c0-174">Com **procurar** selecionado, digite `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="963c0-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="963c0-175">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="963c0-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="963c0-176">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="963c0-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="963c0-177">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="963c0-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="963c0-178">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="963c0-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="963c0-179">Adicionar um :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="963c0-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="963c0-180">No `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="963c0-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="963c0-181">Adicionar serviços e um ponto de extremidade para o :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="963c0-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="963c0-182">No projeto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="963c0-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="963c0-183">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="963c0-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="963c0-184">Adicionar :::no-loc(SignalR)::: e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="963c0-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="963c0-185">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="963c0-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="963c0-186">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="963c0-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="963c0-187">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="963c0-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="963c0-188">Adicionar :::no-loc(Razor)::: código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="963c0-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="963c0-189">No projeto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="963c0-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="963c0-190">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="963c0-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="963c0-191">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="963c0-191">Run the app</span></span>

1. <span data-ttu-id="963c0-192">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="963c0-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="963c0-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="963c0-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="963c0-194">Em **Gerenciador de soluções** , selecione o `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="963c0-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="963c0-195">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="963c0-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="963c0-196">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="963c0-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="963c0-197">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="963c0-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="963c0-198">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="963c0-198">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="963c0-200">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="963c0-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="963c0-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="963c0-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="963c0-202">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="963c0-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="963c0-203">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="963c0-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="963c0-204">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="963c0-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="963c0-205">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="963c0-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="963c0-206">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="963c0-206">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="963c0-208">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="963c0-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="963c0-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="963c0-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="963c0-210">Na barra lateral da **solução** , selecione o `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="963c0-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="963c0-211">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="963c0-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="963c0-212">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="963c0-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="963c0-213">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="963c0-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="963c0-214">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="963c0-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="963c0-216">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="963c0-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="963c0-217">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="963c0-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="963c0-218">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="963c0-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="963c0-219">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="963c0-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="963c0-220">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="963c0-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="963c0-221">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="963c0-221">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="963c0-223">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="963c0-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="963c0-224">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="963c0-224">Next steps</span></span>

<span data-ttu-id="963c0-225">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="963c0-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="963c0-226">Criar um :::no-loc(Blazor WebAssembly)::: projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="963c0-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="963c0-227">Adicionar a :::no-loc(SignalR)::: biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="963c0-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="963c0-228">Adicionar um :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="963c0-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="963c0-229">Adicionar :::no-loc(SignalR)::: serviços e um ponto de extremidade para o :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="963c0-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="963c0-230">Adicionar :::no-loc(Razor)::: código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="963c0-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="963c0-231">Para saber mais sobre a criação de :::no-loc(Blazor)::: aplicativos, consulte a :::no-loc(Blazor)::: documentação:</span><span class="sxs-lookup"><span data-stu-id="963c0-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="963c0-232"><xref:blazor/index>
> [Autenticação de token de portador com :::no-loc(Identity)::: eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="963c0-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="963c0-233">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="963c0-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="963c0-234">:::no-loc(SignalR)::: negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="963c0-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
