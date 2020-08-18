---
title: ASP.NET Core de depuração Blazor WebAssembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/17/2020
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
uid: blazor/debug
ms.openlocfilehash: ac649b80a6cb1106de8acbb4764b53017afaa238
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504587"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c728a-103">ASP.NET Core de depuração Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c728a-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="c728a-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="c728a-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="c728a-105">Blazor WebAssembly os aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="c728a-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="c728a-106">Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c728a-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="c728a-107">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="c728a-107">Available scenarios include:</span></span>

* <span data-ttu-id="c728a-108">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="c728a-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="c728a-109">Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="c728a-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="c728a-110">Etapa única (<kbd>F10</kbd>) por meio do código.</span><span class="sxs-lookup"><span data-stu-id="c728a-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="c728a-111">Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c728a-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="c728a-112">Na exibição *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="c728a-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="c728a-113">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c728a-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="c728a-114">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="c728a-114">For now, you *can't*:</span></span>

* <span data-ttu-id="c728a-115">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="c728a-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="c728a-116">Acerte os pontos de interrupção durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="c728a-116">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="c728a-117">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c728a-117">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="c728a-118">Continuaremos a melhorar a experiência de depuração em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="c728a-118">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c728a-119">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c728a-119">Prerequisites</span></span>

<span data-ttu-id="c728a-120">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="c728a-120">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="c728a-121">Google Chrome (versão 70 ou posterior) (padrão)</span><span class="sxs-lookup"><span data-stu-id="c728a-121">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="c728a-122">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="c728a-122">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="c728a-123">Habilitar depuração para Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c728a-123">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="c728a-124">Para habilitar a depuração para um Blazor WebAssembly aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="c728a-124">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="c728a-125">Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="c728a-125">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="c728a-126">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="c728a-126">The `inspectUri` property:</span></span>

* <span data-ttu-id="c728a-127">Permite que o IDE detecte que o aplicativo é um Blazor WebAssembly aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c728a-127">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="c728a-128">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="c728a-128">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="c728a-129">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="c728a-129">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="c728a-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c728a-130">Visual Studio</span></span>

<span data-ttu-id="c728a-131">Para depurar um Blazor WebAssembly aplicativo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c728a-131">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="c728a-132">Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="c728a-132">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="c728a-133">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="c728a-133">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c728a-134">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="c728a-134">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="c728a-135">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="c728a-135">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="c728a-136">Defina um ponto de interrupção no `Pages/Counter.razor` no `IncrementCount` método.</span><span class="sxs-lookup"><span data-stu-id="c728a-136">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="c728a-137">Navegue até a **`Counter`** guia e selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="c728a-137">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="c728a-139">Confira o valor do `currentCount` campo na janela locais:</span><span class="sxs-lookup"><span data-stu-id="c728a-139">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="c728a-141">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="c728a-141">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="c728a-142">Ao depurar seu Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="c728a-142">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="c728a-143">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="c728a-143">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="c728a-144">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="c728a-144">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="c728a-145">Navegue até a **`Fetch Data`** guia para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="c728a-145">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="c728a-147">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="c728a-147">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="c728a-149">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.</span><span class="sxs-lookup"><span data-stu-id="c728a-149">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="c728a-150">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c728a-150">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="c728a-151">Depurar autônomo Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c728a-151">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="c728a-152">Abra o Blazor WebAssembly aplicativo autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="c728a-152">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="c728a-153">Você pode receber a seguinte notificação de que a configuração adicional é necessária para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="c728a-153">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="c728a-155">Se você receber a notificação:</span><span class="sxs-lookup"><span data-stu-id="c728a-155">If you receive the notification:</span></span>

   * <span data-ttu-id="c728a-156">Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada.</span><span class="sxs-lookup"><span data-stu-id="c728a-156">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="c728a-157">Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .</span><span class="sxs-lookup"><span data-stu-id="c728a-157">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="c728a-158">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="c728a-158">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="c728a-159">Abra as configurações na barra de menus (configurações de preferências de**arquivo**  >  **Preferences**  >  **Settings**).</span><span class="sxs-lookup"><span data-stu-id="c728a-159">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="c728a-160">Pesquise usando as palavras-chave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="c728a-160">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="c728a-161">Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada.</span><span class="sxs-lookup"><span data-stu-id="c728a-161">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="c728a-162">Se a opção para habilitar a depuração de visualização não estiver presente, atualize para a versão mais recente do VS Code ou instale a [extensão do depurador do JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versões 1,46 ou anteriores).</span><span class="sxs-lookup"><span data-stu-id="c728a-162">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="c728a-163">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="c728a-163">Reload the window.</span></span>

1. <span data-ttu-id="c728a-164">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="c728a-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c728a-165">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="c728a-165">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="c728a-166">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="c728a-166">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="c728a-167">Quando solicitado, selecione a opção de \*\* Blazor WebAssembly depuração\*\* para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="c728a-167">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista de opções de depuração disponíveis](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="c728a-169">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="c728a-169">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="c728a-170">Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="c728a-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="c728a-172">Depuração hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c728a-172">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="c728a-173">Abra a Blazor WebAssembly pasta de solução do aplicativo hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="c728a-173">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="c728a-174">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="c728a-174">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="c728a-175">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="c728a-175">Select **Yes**.</span></span>

   ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="c728a-177">Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="c728a-177">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="c728a-178">Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="c728a-178">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="c728a-179">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="c728a-179">Attach to an existing debugging session</span></span>

<span data-ttu-id="c728a-180">Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="c728a-180">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="c728a-181">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="c728a-181">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="c728a-182">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="c728a-182">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="c728a-183">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="c728a-183">Launch configuration options</span></span>

<span data-ttu-id="c728a-184">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="c728a-184">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="c728a-185">Opção</span><span class="sxs-lookup"><span data-stu-id="c728a-185">Option</span></span>    | <span data-ttu-id="c728a-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="c728a-186">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="c728a-187">Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor WebAssembly aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="c728a-187">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="c728a-188">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="c728a-188">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="c728a-189">O padrão é `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c728a-189">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="c728a-190">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="c728a-190">The browser to launch for the debugging session.</span></span> <span data-ttu-id="c728a-191">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="c728a-191">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="c728a-192">O padrão é `chrome`.</span><span class="sxs-lookup"><span data-stu-id="c728a-192">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="c728a-193">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="c728a-193">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="c728a-194">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="c728a-194">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="c728a-195">Deve ser definido como `true` se estiver iniciando e depurando um Blazor WebAssembly aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="c728a-195">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="c728a-196">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="c728a-196">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="c728a-197">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="c728a-197">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="c728a-198">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="c728a-198">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="c728a-199">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="c728a-199">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="c728a-200">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="c728a-200">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="c728a-201">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="c728a-201">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="c728a-202">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c728a-202">The working directory to launch the app under.</span></span> <span data-ttu-id="c728a-203">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="c728a-203">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="c728a-204">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="c728a-204">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="c728a-205">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="c728a-205">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="c728a-206">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="c728a-206">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="c728a-207">Iniciar e depurar um Blazor WebAssembly aplicativo autônomo</span><span class="sxs-lookup"><span data-stu-id="c728a-207">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="c728a-208">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="c728a-208">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="c728a-209">Iniciar e depurar um Blazor WebAssembly aplicativo hospedado com o Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c728a-209">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="c728a-210">A configuração do navegador usa como padrão o Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="c728a-210">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="c728a-211">Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` .</span><span class="sxs-lookup"><span data-stu-id="c728a-211">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="c728a-212">Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .</span><span class="sxs-lookup"><span data-stu-id="c728a-212">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="c728a-213">No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="c728a-213">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="c728a-214">A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="c728a-214">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="c728a-215">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="c728a-215">Debug in the browser</span></span>

1. <span data-ttu-id="c728a-216">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c728a-216">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="c728a-217">Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="c728a-217">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="c728a-218">No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="c728a-218">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="c728a-219">O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão.</span><span class="sxs-lookup"><span data-stu-id="c728a-219">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="c728a-220">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta.</span><span class="sxs-lookup"><span data-stu-id="c728a-220">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="c728a-221">Siga as instruções para seu navegador, que abre uma nova janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="c728a-221">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="c728a-222">Feche a janela anterior do navegador.</span><span class="sxs-lookup"><span data-stu-id="c728a-222">Close the previous browser window.</span></span>

1. <span data-ttu-id="c728a-223">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração (<kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) abrirá uma nova guia do depurador.</span><span class="sxs-lookup"><span data-stu-id="c728a-223">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="c728a-224">Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.</span><span class="sxs-lookup"><span data-stu-id="c728a-224">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="c728a-225">No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado.</span><span class="sxs-lookup"><span data-stu-id="c728a-225">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="c728a-226">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="c728a-226">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="c728a-227">Blazor fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="c728a-227">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="c728a-228">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="c728a-228">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="c728a-229">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="c728a-229">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="c728a-230">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="c728a-230">Browser source maps</span></span>

<span data-ttu-id="c728a-231">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="c728a-231">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="c728a-232">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="c728a-232">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="c728a-233">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="c728a-233">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c728a-234">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="c728a-234">Troubleshoot</span></span>

<span data-ttu-id="c728a-235">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="c728a-235">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="c728a-236">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="c728a-236">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="c728a-237">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="c728a-237">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="c728a-238">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c728a-238">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="c728a-239">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="c728a-239">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="c728a-240">O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**.</span><span class="sxs-lookup"><span data-stu-id="c728a-240">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="c728a-241">Essa é a configuração padrão para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c728a-241">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="c728a-242">Se a depuração não estiver funcionando, confirme se a opção está selecionada.</span><span class="sxs-lookup"><span data-stu-id="c728a-242">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="c728a-243">Pontos de interrupção em `OnInitialized{Async}` não atingido</span><span class="sxs-lookup"><span data-stu-id="c728a-243">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="c728a-244">O Blazor proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos.</span><span class="sxs-lookup"><span data-stu-id="c728a-244">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="c728a-245">É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido.</span><span class="sxs-lookup"><span data-stu-id="c728a-245">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="c728a-246">Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c728a-246">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="c728a-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="c728a-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="c728a-248"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="c728a-248"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-timeout"></a><span data-ttu-id="c728a-249">Tempo limite do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c728a-249">Visual Studio timeout</span></span>

<span data-ttu-id="c728a-250">Se o Visual Studio lançar uma exceção de que o adaptador de depuração falhou ao ser iniciado mencionando que o tempo limite foi atingido, você poderá ajustar o tempo limite com uma configuração de registro:</span><span class="sxs-lookup"><span data-stu-id="c728a-250">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="c728a-251">O `{TIMEOUT}` espaço reservado no comando anterior é em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="c728a-251">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="c728a-252">Por exemplo, um minuto é atribuído como `60000` .</span><span class="sxs-lookup"><span data-stu-id="c728a-252">For example, one minute is assigned as `60000`.</span></span>
