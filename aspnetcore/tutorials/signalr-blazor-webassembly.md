---
title: Usar ASP.NET Core SignalR comBlazor WebAssembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021621"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="14cab-103">Usar ASP.NET Core SignalR comBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="14cab-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="14cab-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="14cab-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="14cab-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="14cab-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="14cab-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="14cab-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14cab-107">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="14cab-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="14cab-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="14cab-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="14cab-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="14cab-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="14cab-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="14cab-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="14cab-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="14cab-111">Add Razor component code for chat</span></span>

<span data-ttu-id="14cab-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="14cab-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="14cab-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14cab-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="14cab-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="14cab-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14cab-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14cab-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14cab-116">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="14cab-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="14cab-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14cab-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14cab-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="14cab-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="14cab-119">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="14cab-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="14cab-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="14cab-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="14cab-121">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="14cab-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="14cab-122">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="14cab-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14cab-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14cab-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="14cab-124">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="14cab-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="14cab-125">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="14cab-125">Create a new project.</span></span>

1. <span data-ttu-id="14cab-126">Selecione \*\* Blazor aplicativo\*\* e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="14cab-127">Digite `BlazorSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="14cab-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="14cab-128">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="14cab-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="14cab-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-129">Select **Create**.</span></span>

1. <span data-ttu-id="14cab-130">Escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="14cab-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="14cab-131">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="14cab-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="14cab-132">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14cab-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14cab-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="14cab-134">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="14cab-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="14cab-135">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="14cab-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="14cab-136">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="14cab-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="14cab-137">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="14cab-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14cab-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="14cab-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="14cab-139">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="14cab-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="14cab-140">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="14cab-141">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="14cab-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="14cab-142">Escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="14cab-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="14cab-143">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-143">Select **Next**.</span></span>

1. <span data-ttu-id="14cab-144">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="14cab-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="14cab-145">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="14cab-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="14cab-146">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-146">Select **Next**.</span></span>

1. <span data-ttu-id="14cab-147">No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="14cab-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="14cab-148">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-148">Select **Create**.</span></span>

   <span data-ttu-id="14cab-149">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="14cab-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="14cab-150">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="14cab-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="14cab-151">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="14cab-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="14cab-152">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="14cab-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="14cab-153">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="14cab-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="14cab-154">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="14cab-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14cab-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14cab-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="14cab-156">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="14cab-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="14cab-157">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="14cab-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="14cab-158">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="14cab-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="14cab-159">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="14cab-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="14cab-160">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="14cab-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="14cab-161">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="14cab-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14cab-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14cab-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="14cab-163">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="14cab-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14cab-164">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="14cab-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="14cab-165">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="14cab-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="14cab-166">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="14cab-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="14cab-167">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="14cab-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="14cab-168">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="14cab-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="14cab-169">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="14cab-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="14cab-170">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="14cab-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="14cab-171">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="14cab-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="14cab-172">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="14cab-172">Add a SignalR hub</span></span>

<span data-ttu-id="14cab-173">No `BlazorSignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="14cab-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="14cab-174">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="14cab-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="14cab-175">No projeto `BlazorSignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="14cab-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="14cab-176">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="14cab-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="14cab-177">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="14cab-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="14cab-178">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="14cab-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="14cab-179">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="14cab-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="14cab-180">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="14cab-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="14cab-181">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="14cab-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="14cab-182">No projeto `BlazorSignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="14cab-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="14cab-183">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="14cab-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="14cab-184">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="14cab-184">Run the app</span></span>

1. <span data-ttu-id="14cab-185">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="14cab-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14cab-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14cab-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="14cab-187">Em **Gerenciador de soluções**, selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="14cab-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="14cab-188">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="14cab-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="14cab-189">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="14cab-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14cab-190">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="14cab-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="14cab-191">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="14cab-191">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="14cab-193">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="14cab-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="14cab-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14cab-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="14cab-195">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="14cab-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="14cab-196">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="14cab-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="14cab-197">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="14cab-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14cab-198">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="14cab-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="14cab-199">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="14cab-199">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="14cab-201">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="14cab-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14cab-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="14cab-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="14cab-203">Na barra lateral da **solução** , selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="14cab-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="14cab-204">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="14cab-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="14cab-205">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="14cab-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14cab-206">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="14cab-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="14cab-207">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="14cab-207">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="14cab-209">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="14cab-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="14cab-210">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="14cab-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="14cab-211">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="14cab-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="14cab-212">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="14cab-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="14cab-213">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="14cab-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="14cab-214">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="14cab-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="14cab-216">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="14cab-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="14cab-217">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="14cab-217">Next steps</span></span>

<span data-ttu-id="14cab-218">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="14cab-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14cab-219">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="14cab-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="14cab-220">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="14cab-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="14cab-221">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="14cab-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="14cab-222">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="14cab-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="14cab-223">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="14cab-223">Add Razor component code for chat</span></span>

<span data-ttu-id="14cab-224">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="14cab-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="14cab-225">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="14cab-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="14cab-226">SignalRnegociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="14cab-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
