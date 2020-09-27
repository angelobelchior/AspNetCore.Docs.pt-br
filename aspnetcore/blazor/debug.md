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
ms.openlocfilehash: e12b0e6d1bf9eab751f6605b9a156f637f2b0c0f
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393828"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="b7a8b-103">ASP.NET Core de depuração Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b7a8b-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="b7a8b-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b7a8b-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="b7a8b-105">Blazor WebAssembly os aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="b7a8b-106">Você também pode depurar seu aplicativo usando os seguintes IDEs (ambientes de desenvolvimento integrado):</span><span class="sxs-lookup"><span data-stu-id="b7a8b-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="b7a8b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7a8b-107">Visual Studio</span></span>
* <span data-ttu-id="b7a8b-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7a8b-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="b7a8b-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7a8b-109">Visual Studio Code</span></span>

<span data-ttu-id="b7a8b-110">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-110">Available scenarios include:</span></span>

* <span data-ttu-id="b7a8b-111">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b7a8b-112">Execute o aplicativo com suporte para depuração em IDEs.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="b7a8b-113">Etapa única pelo código.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-113">Single-step through the code.</span></span>
* <span data-ttu-id="b7a8b-114">Retome a execução de código com um atalho de teclado em IDEs.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="b7a8b-115">Na janela *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="b7a8b-116">Consulte a pilha de chamadas, incluindo cadeias de chamada entre JavaScript e .NET.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="b7a8b-117">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-117">For now, you *can't*:</span></span>

* <span data-ttu-id="b7a8b-118">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b7a8b-119">Acerte os pontos de interrupção durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7a8b-120">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7a8b-121">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b7a8b-121">Prerequisites</span></span>

<span data-ttu-id="b7a8b-122">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b7a8b-123">Google Chrome (versão 70 ou posterior) (padrão)</span><span class="sxs-lookup"><span data-stu-id="b7a8b-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="b7a8b-124">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="b7a8b-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="b7a8b-125">Visual Studio para Mac requer a versão 8,8 (Build 1532) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="b7a8b-126">Instale a versão mais recente do Visual Studio para Mac selecionando o botão **baixar Visual Studio para Mac** na [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="b7a8b-127">Selecione o canal de *Visualização* de dentro do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="b7a8b-128">Para obter mais informações, consulte [instalar uma versão de visualização do Visual Studio para Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="b7a8b-129">Atualmente, o Apple Safari no macOS não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="b7a8b-130">Habilitar depuração</span><span class="sxs-lookup"><span data-stu-id="b7a8b-130">Enable debugging</span></span>

<span data-ttu-id="b7a8b-131">Para habilitar a depuração para um Blazor WebAssembly aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b7a8b-132">Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b7a8b-133">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="b7a8b-134">Permite que o IDE detecte que o aplicativo é um Blazor WebAssembly aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b7a8b-135">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="b7a8b-136">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7a8b-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7a8b-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b7a8b-138">Para depurar um Blazor WebAssembly aplicativo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b7a8b-139">Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b7a8b-140">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b7a8b-141">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="b7a8b-142">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="b7a8b-143">No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="b7a8b-144">No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="b7a8b-145">No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="b7a8b-146">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b7a8b-147">Ao depurar um Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="b7a8b-148">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b7a8b-149">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b7a8b-150">Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="b7a8b-151">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="b7a8b-152">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="b7a8b-153">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7a8b-154">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="b7a8b-155">Se o aplicativo estiver hospedado em um [caminho base de aplicativo](xref:blazor/host-and-deploy/index#app-base-path) diferente do que o `/` , atualize as seguintes propriedades no `Properties/launchSettings.json` para refletir o caminho base do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-155">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="b7a8b-156">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-156">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="b7a8b-157">`inspectUri` de cada perfil:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-157">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="b7a8b-158">Os espaços reservados nas configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-158">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="b7a8b-159">`{INSECURE PORT}`: A porta não segura.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-159">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="b7a8b-160">Um valor aleatório é fornecido por padrão, mas uma porta personalizada é permitida.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-160">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="b7a8b-161">`{APP BASE PATH}`: O caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-161">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="b7a8b-162">`{SECURE PORT}`: A porta segura.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-162">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="b7a8b-163">Um valor aleatório é fornecido por padrão, mas uma porta personalizada é permitida.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="b7a8b-164">`{PROFILE 1, 2, ... N}`: Iniciar perfis de configurações.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-164">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="b7a8b-165">Normalmente, um aplicativo especifica mais de um perfil por padrão (por exemplo, um perfil para IIS Express e um perfil de projeto, que é usado pelo servidor Kestrel).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-165">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="b7a8b-166">Nos exemplos a seguir, o aplicativo é hospedado em `/OAT` com um caminho base de aplicativo configurado em `wwwroot/index.html` como `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="b7a8b-166">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="b7a8b-167">Para obter informações sobre como usar um caminho básico do aplicativo personalizado para Blazor WebAssembly aplicativos, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-167">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7a8b-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7a8b-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="b7a8b-169">Depurar autônomo Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b7a8b-169">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="b7a8b-170">Abra o Blazor WebAssembly aplicativo autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-170">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="b7a8b-171">Você pode receber uma notificação de que a configuração adicional é necessária para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-171">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="b7a8b-172">A configuração adicional é necessária para depurar Blazor WebAssembly aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-172">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="b7a8b-173">Se você receber a notificação:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-173">If you receive the notification:</span></span>

   * <span data-ttu-id="b7a8b-174">Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-174">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="b7a8b-175">Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-175">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="b7a8b-176">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-176">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="b7a8b-177">Abra as configurações na barra de menus (configurações de preferências de**arquivo**  >  **Preferences**  >  **Settings**).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-177">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="b7a8b-178">Pesquise usando as palavras-chave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-178">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="b7a8b-179">Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-179">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="b7a8b-180">Se a opção para habilitar a depuração de visualização não estiver presente, atualize para a versão mais recente do VS Code ou instale a [extensão do depurador do JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versões 1,46 ou anteriores).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-180">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="b7a8b-181">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-181">Reload the window.</span></span>

1. <span data-ttu-id="b7a8b-182">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-182">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b7a8b-183">Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-183">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="b7a8b-184">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-184">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="b7a8b-185">Quando solicitado, selecione a opção de \*\* Blazor WebAssembly depuração\*\* para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-185">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="b7a8b-186">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-186">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="b7a8b-187">No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-187">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="b7a8b-188">No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-188">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="b7a8b-189">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-189">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7a8b-190">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-190">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="b7a8b-191">Depuração hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b7a8b-191">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="b7a8b-192">Abra a Blazor WebAssembly pasta de solução do aplicativo hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-192">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="b7a8b-193">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-193">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="b7a8b-194">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-194">Select **Yes**.</span></span>

   > <span data-ttu-id="b7a8b-195">Os ativos necessários para compilar e depurar estão ausentes em ' {nome do aplicativo} '.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-195">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="b7a8b-196">Deseja adicioná-los?</span><span class="sxs-lookup"><span data-stu-id="b7a8b-196">Add them?</span></span>

1. <span data-ttu-id="b7a8b-197">Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-197">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="b7a8b-198">Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-198">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="b7a8b-199">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="b7a8b-199">Attach to an existing debugging session</span></span>

<span data-ttu-id="b7a8b-200">Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-200">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="b7a8b-201">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-201">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="b7a8b-202">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-202">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="b7a8b-203">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="b7a8b-203">Launch configuration options</span></span>

<span data-ttu-id="b7a8b-204">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-204">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="b7a8b-205">Opção</span><span class="sxs-lookup"><span data-stu-id="b7a8b-205">Option</span></span>    | <span data-ttu-id="b7a8b-206">Descrição</span><span class="sxs-lookup"><span data-stu-id="b7a8b-206">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="b7a8b-207">Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor WebAssembly aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-207">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="b7a8b-208">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-208">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="b7a8b-209">Assume o padrão de `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-209">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="b7a8b-210">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-210">The browser to launch for the debugging session.</span></span> <span data-ttu-id="b7a8b-211">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-211">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="b7a8b-212">Assume o padrão de `chrome`.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-212">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="b7a8b-213">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-213">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="b7a8b-214">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-214">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="b7a8b-215">Deve ser definido como `true` se estiver iniciando e depurando um Blazor WebAssembly aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-215">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="b7a8b-216">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-216">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="b7a8b-217">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-217">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="b7a8b-218">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-218">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="b7a8b-219">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-219">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="b7a8b-220">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-220">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="b7a8b-221">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-221">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="b7a8b-222">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-222">The working directory to launch the app under.</span></span> <span data-ttu-id="b7a8b-223">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-223">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="b7a8b-224">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-224">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="b7a8b-225">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-225">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="b7a8b-226">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="b7a8b-226">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="b7a8b-227">Iniciar e depurar um Blazor WebAssembly aplicativo autônomo</span><span class="sxs-lookup"><span data-stu-id="b7a8b-227">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="b7a8b-228">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="b7a8b-228">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="b7a8b-229">Iniciar e depurar um Blazor WebAssembly aplicativo hospedado com o Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="b7a8b-229">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="b7a8b-230">A configuração do navegador usa como padrão o Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-230">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="b7a8b-231">Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-231">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="b7a8b-232">Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-232">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="b7a8b-233">No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-233">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="b7a8b-234">A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-234">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7a8b-235">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7a8b-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b7a8b-236">Para depurar um Blazor WebAssembly aplicativo no Visual Studio para Mac:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-236">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="b7a8b-237">Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-237">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b7a8b-238">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b7a8b-239">Não há suporte para **Iniciar sem depuração** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-239">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="b7a8b-240">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-240">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="b7a8b-241">O Google Chrome ou o Microsoft Edge deve ser o navegador selecionado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-241">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="b7a8b-242">No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-242">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="b7a8b-243">No navegador, navegue até a `Counter` página e selecione o botão **Click me** para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-243">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="b7a8b-244">No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-244">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="b7a8b-245">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-245">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="b7a8b-246">Ao depurar um Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-246">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="b7a8b-247">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-247">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b7a8b-248">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-248">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b7a8b-249">Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-249">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="b7a8b-250">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="b7a8b-251">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="b7a8b-252">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-252">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7a8b-253">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-253">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="b7a8b-254">Para obter mais informações, consulte [Depurando com Visual Studio para Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-254">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="b7a8b-255">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="b7a8b-255">Debug in the browser</span></span>

<span data-ttu-id="b7a8b-256">*As diretrizes nesta seção aplicam-se ao Google Chrome e ao Microsoft Edge em execução no Windows.*</span><span class="sxs-lookup"><span data-stu-id="b7a8b-256">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="b7a8b-257">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-257">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b7a8b-258">Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-258">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="b7a8b-259">No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-259">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="b7a8b-260">O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-260">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="b7a8b-261">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-261">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="b7a8b-262">Siga as instruções para seu navegador, que abre uma nova janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-262">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="b7a8b-263">Feche a janela anterior do navegador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-263">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="b7a8b-264">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração na etapa anterior abrirá uma nova guia do depurador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-264">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="b7a8b-265">Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-265">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="b7a8b-266">No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-266">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="b7a8b-267">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-267">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="b7a8b-268">Blazor fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-268">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b7a8b-269">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-269">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b7a8b-270">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="b7a8b-270">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b7a8b-271">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="b7a8b-271">Browser source maps</span></span>

<span data-ttu-id="b7a8b-272">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-272">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b7a8b-273">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-273">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b7a8b-274">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-274">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b7a8b-275">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b7a8b-275">Troubleshoot</span></span>

<span data-ttu-id="b7a8b-276">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-276">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="b7a8b-277">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-277">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b7a8b-278">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-278">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="b7a8b-279">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-279">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="b7a8b-280">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-280">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="b7a8b-281">O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-281">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="b7a8b-282">Essa é a configuração padrão para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-282">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="b7a8b-283">Se a depuração não estiver funcionando, confirme se a opção está selecionada.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-283">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="b7a8b-284">Pontos de interrupção em `OnInitialized{Async}` não atingido</span><span class="sxs-lookup"><span data-stu-id="b7a8b-284">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="b7a8b-285">O Blazor proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-285">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="b7a8b-286">É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-286">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="b7a8b-287">Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-287">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="b7a8b-288"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-288"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="b7a8b-289"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-289"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="b7a8b-290">Tempo limite do Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="b7a8b-290">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="b7a8b-291">Se o Visual Studio lançar uma exceção de que o adaptador de depuração falhou ao ser iniciado mencionando que o tempo limite foi atingido, você poderá ajustar o tempo limite com uma configuração de registro:</span><span class="sxs-lookup"><span data-stu-id="b7a8b-291">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="b7a8b-292">O `{TIMEOUT}` espaço reservado no comando anterior é em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="b7a8b-292">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="b7a8b-293">Por exemplo, um minuto é atribuído como `60000` .</span><span class="sxs-lookup"><span data-stu-id="b7a8b-293">For example, one minute is assigned as `60000`.</span></span>
