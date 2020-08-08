---
title: ASP.NET Core de depuraçãoBlazor WebAssembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
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
ms.openlocfilehash: 225916411550cc8e89c604e1426316843bb0ff52
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014536"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="03c43-103">ASP.NET Core de depuraçãoBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="03c43-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="03c43-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="03c43-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="03c43-105">Blazor WebAssemblyos aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="03c43-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="03c43-106">Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="03c43-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="03c43-107">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="03c43-107">Available scenarios include:</span></span>

* <span data-ttu-id="03c43-108">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="03c43-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="03c43-109">Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="03c43-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="03c43-110">Etapa única (<kbd>F10</kbd>) por meio do código.</span><span class="sxs-lookup"><span data-stu-id="03c43-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="03c43-111">Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="03c43-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="03c43-112">Na exibição *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="03c43-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="03c43-113">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="03c43-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="03c43-114">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="03c43-114">For now, you *can't*:</span></span>

* <span data-ttu-id="03c43-115">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="03c43-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="03c43-116">Pontos de interrupção de acesso durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03c43-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="03c43-117">Continuaremos a melhorar a experiência de depuração em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="03c43-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="03c43-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="03c43-118">Prerequisites</span></span>

<span data-ttu-id="03c43-119">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="03c43-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="03c43-120">Google Chrome (versão 70 ou posterior) (padrão)</span><span class="sxs-lookup"><span data-stu-id="03c43-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="03c43-121">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="03c43-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="03c43-122">Habilitar depuração para Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03c43-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="03c43-123">Para habilitar a depuração para um Blazor WebAssembly aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="03c43-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="03c43-124">Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="03c43-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="03c43-125">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="03c43-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="03c43-126">Permite que o IDE detecte que o aplicativo é um Blazor WebAssembly aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03c43-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="03c43-127">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="03c43-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="03c43-128">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="03c43-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="03c43-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03c43-129">Visual Studio</span></span>

<span data-ttu-id="03c43-130">Para depurar um Blazor WebAssembly aplicativo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="03c43-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="03c43-131">Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="03c43-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="03c43-132">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="03c43-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="03c43-133">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="03c43-133">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span>

1. <span data-ttu-id="03c43-134">Defina um ponto de interrupção no `Pages/Counter.razor` no `IncrementCount` método.</span><span class="sxs-lookup"><span data-stu-id="03c43-134">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="03c43-135">Navegue até a **`Counter`** guia e selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="03c43-135">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="03c43-137">Confira o valor do `currentCount` campo na janela locais:</span><span class="sxs-lookup"><span data-stu-id="03c43-137">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="03c43-139">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="03c43-139">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="03c43-140">Ao depurar seu Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="03c43-140">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="03c43-141">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="03c43-141">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="03c43-142">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="03c43-142">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="03c43-143">Navegue até a **`Fetch Data`** guia para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="03c43-143">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="03c43-145">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="03c43-145">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="03c43-147">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.</span><span class="sxs-lookup"><span data-stu-id="03c43-147">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="03c43-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03c43-148">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="03c43-149">Depurar autônomoBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="03c43-149">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="03c43-150">Abra o Blazor WebAssembly aplicativo autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="03c43-150">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="03c43-151">Você pode receber a seguinte notificação de que a configuração adicional é necessária para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="03c43-151">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="03c43-153">Se você receber a notificação:</span><span class="sxs-lookup"><span data-stu-id="03c43-153">If you receive the notification:</span></span>

   * <span data-ttu-id="03c43-154">Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada.</span><span class="sxs-lookup"><span data-stu-id="03c43-154">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="03c43-155">Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .</span><span class="sxs-lookup"><span data-stu-id="03c43-155">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="03c43-156">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="03c43-156">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="03c43-157">Abra as configurações na barra de menus (configurações de preferências de**arquivo**  >  **Preferences**  >  **Settings**).</span><span class="sxs-lookup"><span data-stu-id="03c43-157">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="03c43-158">Pesquise usando as palavras-chave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="03c43-158">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="03c43-159">Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada.</span><span class="sxs-lookup"><span data-stu-id="03c43-159">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="03c43-160">Se a opção para habilitar a depuração de visualização não estiver presente, atualize para a versão mais recente do VS Code ou instale a [extensão do depurador do JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versões 1,46 ou anteriores).</span><span class="sxs-lookup"><span data-stu-id="03c43-160">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="03c43-161">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="03c43-161">Reload the window.</span></span>

1. <span data-ttu-id="03c43-162">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="03c43-162">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="03c43-163">Não há suporte para **executar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="03c43-163">**Run Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span>

1. <span data-ttu-id="03c43-164">Quando solicitado, selecione a opção de \*\* Blazor WebAssembly depuração\*\* para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="03c43-164">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista de opções de depuração disponíveis](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="03c43-166">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="03c43-166">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="03c43-167">Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="03c43-167">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="03c43-169">Depuração hospedadaBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="03c43-169">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="03c43-170">Abra a Blazor WebAssembly pasta de solução do aplicativo hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="03c43-170">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="03c43-171">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="03c43-171">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="03c43-172">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="03c43-172">Select **Yes**.</span></span>

   ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="03c43-174">Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="03c43-174">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="03c43-175">Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="03c43-175">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="03c43-176">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="03c43-176">Attach to an existing debugging session</span></span>

<span data-ttu-id="03c43-177">Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="03c43-177">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="03c43-178">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="03c43-178">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="03c43-179">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="03c43-179">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="03c43-180">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="03c43-180">Launch configuration options</span></span>

<span data-ttu-id="03c43-181">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="03c43-181">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="03c43-182">Opção</span><span class="sxs-lookup"><span data-stu-id="03c43-182">Option</span></span>    | <span data-ttu-id="03c43-183">Descrição</span><span class="sxs-lookup"><span data-stu-id="03c43-183">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="03c43-184">Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor WebAssembly aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="03c43-184">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="03c43-185">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="03c43-185">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="03c43-186">Assume o padrão de `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="03c43-186">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="03c43-187">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="03c43-187">The browser to launch for the debugging session.</span></span> <span data-ttu-id="03c43-188">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="03c43-188">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="03c43-189">Assume o padrão de `chrome`.</span><span class="sxs-lookup"><span data-stu-id="03c43-189">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="03c43-190">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="03c43-190">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="03c43-191">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="03c43-191">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="03c43-192">Deve ser definido como `true` se estiver iniciando e depurando um Blazor WebAssembly aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="03c43-192">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="03c43-193">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="03c43-193">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="03c43-194">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="03c43-194">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="03c43-195">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="03c43-195">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="03c43-196">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="03c43-196">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="03c43-197">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="03c43-197">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="03c43-198">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="03c43-198">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="03c43-199">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03c43-199">The working directory to launch the app under.</span></span> <span data-ttu-id="03c43-200">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="03c43-200">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="03c43-201">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="03c43-201">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="03c43-202">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="03c43-202">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="03c43-203">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="03c43-203">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="03c43-204">Iniciar e depurar um Blazor WebAssembly aplicativo autônomo</span><span class="sxs-lookup"><span data-stu-id="03c43-204">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="03c43-205">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="03c43-205">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="03c43-206">Iniciar e depurar um Blazor WebAssembly aplicativo hospedado com o Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="03c43-206">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="03c43-207">A configuração do navegador usa como padrão o Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="03c43-207">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="03c43-208">Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` .</span><span class="sxs-lookup"><span data-stu-id="03c43-208">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="03c43-209">Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .</span><span class="sxs-lookup"><span data-stu-id="03c43-209">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="03c43-210">No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="03c43-210">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="03c43-211">A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="03c43-211">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="03c43-212">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="03c43-212">Debug in the browser</span></span>

1. <span data-ttu-id="03c43-213">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="03c43-213">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="03c43-214">Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="03c43-214">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="03c43-215">No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="03c43-215">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="03c43-216">O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão.</span><span class="sxs-lookup"><span data-stu-id="03c43-216">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="03c43-217">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta.</span><span class="sxs-lookup"><span data-stu-id="03c43-217">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="03c43-218">Siga as instruções para seu navegador, que abre uma nova janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="03c43-218">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="03c43-219">Feche a janela anterior do navegador.</span><span class="sxs-lookup"><span data-stu-id="03c43-219">Close the previous browser window.</span></span>

1. <span data-ttu-id="03c43-220">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração (<kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) abrirá uma nova guia do depurador.</span><span class="sxs-lookup"><span data-stu-id="03c43-220">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="03c43-221">Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.</span><span class="sxs-lookup"><span data-stu-id="03c43-221">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="03c43-222">No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado.</span><span class="sxs-lookup"><span data-stu-id="03c43-222">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="03c43-223">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="03c43-223">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="03c43-224">Blazorfornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="03c43-224">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="03c43-225">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="03c43-225">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="03c43-226">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="03c43-226">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="03c43-227">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="03c43-227">Browser source maps</span></span>

<span data-ttu-id="03c43-228">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="03c43-228">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="03c43-229">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="03c43-229">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="03c43-230">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="03c43-230">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="03c43-231">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="03c43-231">Troubleshoot</span></span>

<span data-ttu-id="03c43-232">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="03c43-232">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="03c43-233">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="03c43-233">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="03c43-234">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="03c43-234">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="03c43-235">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="03c43-235">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="03c43-236">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="03c43-236">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="03c43-237">O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**.</span><span class="sxs-lookup"><span data-stu-id="03c43-237">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="03c43-238">Essa é a configuração padrão para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03c43-238">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="03c43-239">Se a depuração não estiver funcionando, confirme se a opção está selecionada.</span><span class="sxs-lookup"><span data-stu-id="03c43-239">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="03c43-240">Pontos de interrupção em `OnInitialized{Async}` não atingido</span><span class="sxs-lookup"><span data-stu-id="03c43-240">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="03c43-241">O Blazor proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos.</span><span class="sxs-lookup"><span data-stu-id="03c43-241">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="03c43-242">É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido.</span><span class="sxs-lookup"><span data-stu-id="03c43-242">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="03c43-243">Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03c43-243">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="03c43-244"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="03c43-244"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="03c43-245"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="03c43-245"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```
