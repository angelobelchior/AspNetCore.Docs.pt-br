---
title: ASP.NET Core de depuração Blazor WebAssembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 7681deb70610a8fbc27ccda7317b73921646794a
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876770"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="fb769-103">ASP.NET Core de depuração Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fb769-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="fb769-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="fb769-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="fb769-105">Blazor WebAssembly os aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="fb769-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="fb769-106">Você também pode depurar seu aplicativo usando os seguintes IDEs (ambientes de desenvolvimento integrado):</span><span class="sxs-lookup"><span data-stu-id="fb769-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="fb769-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb769-107">Visual Studio</span></span>
* <span data-ttu-id="fb769-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fb769-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="fb769-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb769-109">Visual Studio Code</span></span>

<span data-ttu-id="fb769-110">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="fb769-110">Available scenarios include:</span></span>

* <span data-ttu-id="fb769-111">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fb769-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="fb769-112">Execute o aplicativo com suporte para depuração em IDEs.</span><span class="sxs-lookup"><span data-stu-id="fb769-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="fb769-113">Etapa única pelo código.</span><span class="sxs-lookup"><span data-stu-id="fb769-113">Single-step through the code.</span></span>
* <span data-ttu-id="fb769-114">Retome a execução de código com um atalho de teclado em IDEs.</span><span class="sxs-lookup"><span data-stu-id="fb769-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="fb769-115">Na janela *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="fb769-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="fb769-116">Consulte a pilha de chamadas, incluindo cadeias de chamada entre JavaScript e .NET.</span><span class="sxs-lookup"><span data-stu-id="fb769-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="fb769-117">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="fb769-117">For now, you *can't*:</span></span>

* <span data-ttu-id="fb769-118">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="fb769-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="fb769-119">Acerte os pontos de interrupção durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fb769-120">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fb769-121">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="fb769-121">Prerequisites</span></span>

<span data-ttu-id="fb769-122">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="fb769-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="fb769-123">Google Chrome (versão 70 ou posterior) (padrão)</span><span class="sxs-lookup"><span data-stu-id="fb769-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="fb769-124">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="fb769-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="fb769-125">Visual Studio para Mac requer a versão 8,8 (Build 1532) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="fb769-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="fb769-126">Instale a versão mais recente do Visual Studio para Mac selecionando o botão **baixar Visual Studio para Mac** na [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="fb769-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="fb769-127">Selecione o canal de *Visualização* de dentro do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fb769-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="fb769-128">Para obter mais informações, consulte [instalar uma versão de visualização do Visual Studio para Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="fb769-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="fb769-129">Atualmente, o Apple Safari no macOS não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="fb769-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="fb769-130">Habilitar depuração</span><span class="sxs-lookup"><span data-stu-id="fb769-130">Enable debugging</span></span>

<span data-ttu-id="fb769-131">Para habilitar a depuração para um Blazor WebAssembly aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="fb769-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="fb769-132">Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="fb769-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="fb769-133">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="fb769-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="fb769-134">Permite que o IDE detecte que o aplicativo é um Blazor WebAssembly aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="fb769-135">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="fb769-136">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="fb769-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb769-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb769-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fb769-138">Para depurar um Blazor WebAssembly aplicativo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fb769-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="fb769-139">Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="fb769-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="fb769-140">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="fb769-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fb769-141">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="fb769-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="fb769-142">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="fb769-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="fb769-143">No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="fb769-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="fb769-144">No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fb769-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="fb769-145">No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .</span><span class="sxs-lookup"><span data-stu-id="fb769-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="fb769-146">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="fb769-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="fb769-147">Ao depurar um Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="fb769-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="fb769-148">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="fb769-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="fb769-149">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="fb769-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="fb769-150">Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.</span><span class="sxs-lookup"><span data-stu-id="fb769-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="fb769-151">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="fb769-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="fb769-152">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.</span><span class="sxs-lookup"><span data-stu-id="fb769-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="fb769-153">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fb769-154">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb769-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb769-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

<a id="vscode"></a>

## <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="fb769-156">Depurar autônomo Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fb769-156">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="fb769-157">Abra o Blazor WebAssembly aplicativo autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="fb769-157">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="fb769-158">Você pode receber uma notificação de que a configuração adicional é necessária para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="fb769-158">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="fb769-159">A configuração adicional é necessária para depurar Blazor WebAssembly aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fb769-159">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="fb769-160">Se você receber a notificação:</span><span class="sxs-lookup"><span data-stu-id="fb769-160">If you receive the notification:</span></span>

   * <span data-ttu-id="fb769-161">Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada.</span><span class="sxs-lookup"><span data-stu-id="fb769-161">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="fb769-162">Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .</span><span class="sxs-lookup"><span data-stu-id="fb769-162">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="fb769-163">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="fb769-163">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="fb769-164">Abra as configurações na barra de menus (configurações de preferências de**arquivo**  >  **Preferences**  >  **Settings**).</span><span class="sxs-lookup"><span data-stu-id="fb769-164">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="fb769-165">Pesquise usando as palavras-chave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="fb769-165">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="fb769-166">Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada.</span><span class="sxs-lookup"><span data-stu-id="fb769-166">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="fb769-167">Se a opção para habilitar a depuração de visualização não estiver presente, atualize para a versão mais recente do VS Code ou instale a [extensão do depurador do JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versões 1,46 ou anteriores).</span><span class="sxs-lookup"><span data-stu-id="fb769-167">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="fb769-168">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="fb769-168">Reload the window.</span></span>

1. <span data-ttu-id="fb769-169">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="fb769-169">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fb769-170">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="fb769-170">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="fb769-171">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="fb769-171">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="fb769-172">Quando solicitado, selecione a opção de \*\* Blazor WebAssembly depuração\*\* para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-172">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="fb769-173">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="fb769-173">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="fb769-174">No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="fb769-174">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="fb769-175">No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fb769-175">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="fb769-176">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-176">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fb769-177">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-177">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="fb769-178">Depuração hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fb769-178">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="fb769-179">Abra a Blazor WebAssembly pasta de solução do aplicativo hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="fb769-179">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="fb769-180">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="fb769-180">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="fb769-181">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="fb769-181">Select **Yes**.</span></span>

   > <span data-ttu-id="fb769-182">Os ativos necessários para compilar e depurar estão ausentes em ' {nome do aplicativo} '.</span><span class="sxs-lookup"><span data-stu-id="fb769-182">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="fb769-183">Deseja adicioná-los?</span><span class="sxs-lookup"><span data-stu-id="fb769-183">Add them?</span></span>

1. <span data-ttu-id="fb769-184">Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="fb769-184">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="fb769-185">Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="fb769-185">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="fb769-186">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="fb769-186">Attach to an existing debugging session</span></span>

<span data-ttu-id="fb769-187">Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="fb769-187">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="fb769-188">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="fb769-188">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="fb769-189">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="fb769-189">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="fb769-190">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="fb769-190">Launch configuration options</span></span>

<span data-ttu-id="fb769-191">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="fb769-191">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="fb769-192">Opção</span><span class="sxs-lookup"><span data-stu-id="fb769-192">Option</span></span>    | <span data-ttu-id="fb769-193">Descrição</span><span class="sxs-lookup"><span data-stu-id="fb769-193">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="fb769-194">Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor WebAssembly aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="fb769-194">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="fb769-195">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-195">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="fb769-196">O padrão é `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fb769-196">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="fb769-197">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-197">The browser to launch for the debugging session.</span></span> <span data-ttu-id="fb769-198">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="fb769-198">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="fb769-199">O padrão é `chrome`.</span><span class="sxs-lookup"><span data-stu-id="fb769-199">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="fb769-200">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="fb769-200">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="fb769-201">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="fb769-201">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="fb769-202">Deve ser definido como `true` se estiver iniciando e depurando um Blazor WebAssembly aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="fb769-202">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="fb769-203">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="fb769-203">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="fb769-204">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="fb769-204">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="fb769-205">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="fb769-205">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="fb769-206">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="fb769-206">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="fb769-207">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="fb769-207">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="fb769-208">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="fb769-208">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="fb769-209">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-209">The working directory to launch the app under.</span></span> <span data-ttu-id="fb769-210">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="fb769-210">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="fb769-211">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="fb769-211">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="fb769-212">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="fb769-212">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="fb769-213">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="fb769-213">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="fb769-214">Iniciar e depurar um Blazor WebAssembly aplicativo autônomo</span><span class="sxs-lookup"><span data-stu-id="fb769-214">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="fb769-215">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="fb769-215">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="fb769-216">Iniciar e depurar um Blazor WebAssembly aplicativo hospedado com o Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fb769-216">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="fb769-217">A configuração do navegador usa como padrão o Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="fb769-217">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="fb769-218">Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` .</span><span class="sxs-lookup"><span data-stu-id="fb769-218">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="fb769-219">Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .</span><span class="sxs-lookup"><span data-stu-id="fb769-219">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="fb769-220">No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="fb769-220">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="fb769-221">A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="fb769-221">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb769-222">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fb769-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fb769-223">Para depurar um Blazor WebAssembly aplicativo no Visual Studio para Mac:</span><span class="sxs-lookup"><span data-stu-id="fb769-223">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="fb769-224">Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="fb769-224">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="fb769-225">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="fb769-225">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fb769-226">Não há suporte para **Iniciar sem depuração** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>).</span><span class="sxs-lookup"><span data-stu-id="fb769-226">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="fb769-227">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="fb769-227">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="fb769-228">O Google Chrome ou o Microsoft Edge deve ser o navegador selecionado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-228">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="fb769-229">No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="fb769-229">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="fb769-230">No navegador, navegue até a `Counter` página e selecione o botão **Click me** para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="fb769-230">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="fb769-231">No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .</span><span class="sxs-lookup"><span data-stu-id="fb769-231">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="fb769-232">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="fb769-232">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="fb769-233">Ao depurar um Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="fb769-233">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="fb769-234">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="fb769-234">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="fb769-235">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="fb769-235">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="fb769-236">Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.</span><span class="sxs-lookup"><span data-stu-id="fb769-236">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="fb769-237">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="fb769-237">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="fb769-238">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.</span><span class="sxs-lookup"><span data-stu-id="fb769-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="fb769-239">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fb769-239">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fb769-240">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-240">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="fb769-241">Para obter mais informações, consulte [Depurando com Visual Studio para Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span><span class="sxs-lookup"><span data-stu-id="fb769-241">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="fb769-242">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="fb769-242">Debug in the browser</span></span>

<span data-ttu-id="fb769-243">*As diretrizes nesta seção aplicam-se ao Google Chrome e ao Microsoft Edge em execução no Windows.*</span><span class="sxs-lookup"><span data-stu-id="fb769-243">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="fb769-244">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="fb769-244">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="fb769-245">Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="fb769-245">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="fb769-246">No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="fb769-246">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="fb769-247">O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão.</span><span class="sxs-lookup"><span data-stu-id="fb769-247">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="fb769-248">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta.</span><span class="sxs-lookup"><span data-stu-id="fb769-248">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="fb769-249">Siga as instruções para seu navegador, que abre uma nova janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="fb769-249">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="fb769-250">Feche a janela anterior do navegador.</span><span class="sxs-lookup"><span data-stu-id="fb769-250">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="fb769-251">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração na etapa anterior abrirá uma nova guia do depurador.</span><span class="sxs-lookup"><span data-stu-id="fb769-251">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="fb769-252">Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.</span><span class="sxs-lookup"><span data-stu-id="fb769-252">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="fb769-253">No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado.</span><span class="sxs-lookup"><span data-stu-id="fb769-253">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="fb769-254">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="fb769-254">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="fb769-255">Blazor fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="fb769-255">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="fb769-256">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="fb769-256">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="fb769-257">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="fb769-257">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="fb769-258">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="fb769-258">Browser source maps</span></span>

<span data-ttu-id="fb769-259">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="fb769-259">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="fb769-260">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="fb769-260">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="fb769-261">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="fb769-261">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fb769-262">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="fb769-262">Troubleshoot</span></span>

<span data-ttu-id="fb769-263">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="fb769-263">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="fb769-264">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="fb769-264">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="fb769-265">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fb769-265">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="fb769-266">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fb769-266">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="fb769-267">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="fb769-267">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="fb769-268">O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**.</span><span class="sxs-lookup"><span data-stu-id="fb769-268">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="fb769-269">Essa é a configuração padrão para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fb769-269">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="fb769-270">Se a depuração não estiver funcionando, confirme se a opção está selecionada.</span><span class="sxs-lookup"><span data-stu-id="fb769-270">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="fb769-271">Pontos de interrupção em `OnInitialized{Async}` não atingido</span><span class="sxs-lookup"><span data-stu-id="fb769-271">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="fb769-272">O Blazor proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos.</span><span class="sxs-lookup"><span data-stu-id="fb769-272">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="fb769-273">É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido.</span><span class="sxs-lookup"><span data-stu-id="fb769-273">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="fb769-274">Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb769-274">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="fb769-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="fb769-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="fb769-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="fb769-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="fb769-277">Tempo limite do Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="fb769-277">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="fb769-278">Se o Visual Studio lançar uma exceção de que o adaptador de depuração falhou ao ser iniciado mencionando que o tempo limite foi atingido, você poderá ajustar o tempo limite com uma configuração de registro:</span><span class="sxs-lookup"><span data-stu-id="fb769-278">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="fb769-279">O `{TIMEOUT}` espaço reservado no comando anterior é em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="fb769-279">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="fb769-280">Por exemplo, um minuto é atribuído como `60000` .</span><span class="sxs-lookup"><span data-stu-id="fb769-280">For example, one minute is assigned as `60000`.</span></span>
