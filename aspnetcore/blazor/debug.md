---
title: 'ASP.NET Core de depuração :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Saiba como depurar :::no-loc(Blazor)::: aplicativos.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: b7e246c20bf12f8ddf07cff54864836cb535aa60
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94339991"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="fd941-103">ASP.NET Core de depuração :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="fd941-103">Debug ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

[<span data-ttu-id="fd941-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="fd941-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="fd941-105">:::no-loc(Blazor WebAssembly)::: os aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="fd941-105">:::no-loc(Blazor WebAssembly)::: apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="fd941-106">Você também pode depurar seu aplicativo usando os seguintes IDEs (ambientes de desenvolvimento integrado):</span><span class="sxs-lookup"><span data-stu-id="fd941-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="fd941-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd941-107">Visual Studio</span></span>
* <span data-ttu-id="fd941-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fd941-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="fd941-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fd941-109">Visual Studio Code</span></span>

<span data-ttu-id="fd941-110">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="fd941-110">Available scenarios include:</span></span>

* <span data-ttu-id="fd941-111">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fd941-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="fd941-112">Execute o aplicativo com suporte para depuração em IDEs.</span><span class="sxs-lookup"><span data-stu-id="fd941-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="fd941-113">Etapa única pelo código.</span><span class="sxs-lookup"><span data-stu-id="fd941-113">Single-step through the code.</span></span>
* <span data-ttu-id="fd941-114">Retome a execução de código com um atalho de teclado em IDEs.</span><span class="sxs-lookup"><span data-stu-id="fd941-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="fd941-115">Na janela *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="fd941-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="fd941-116">Consulte a pilha de chamadas, incluindo cadeias de chamada entre JavaScript e .NET.</span><span class="sxs-lookup"><span data-stu-id="fd941-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="fd941-117">Por enquanto, você *não pode* :</span><span class="sxs-lookup"><span data-stu-id="fd941-117">For now, you *can't* :</span></span>

* <span data-ttu-id="fd941-118">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="fd941-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="fd941-119">Acerte os pontos de interrupção durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fd941-120">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fd941-121">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="fd941-121">Prerequisites</span></span>

<span data-ttu-id="fd941-122">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="fd941-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="fd941-123">Google Chrome (versão 70 ou posterior) (padrão)</span><span class="sxs-lookup"><span data-stu-id="fd941-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="fd941-124">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="fd941-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="fd941-125">Verifique se os firewalls ou proxies não bloqueiam a comunicação com o proxy de depuração ( `NodeJS` processo).</span><span class="sxs-lookup"><span data-stu-id="fd941-125">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="fd941-126">Para obter mais informações, consulte a seção [configuração do firewall](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="fd941-126">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="fd941-127">Visual Studio para Mac requer a versão 8,8 (Build 1532) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="fd941-127">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="fd941-128">Instale a versão mais recente do Visual Studio para Mac selecionando o botão **baixar Visual Studio para Mac** na [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="fd941-128">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="fd941-129">Selecione o canal de *Visualização* de dentro do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fd941-129">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="fd941-130">Para obter mais informações, consulte [instalar uma versão de visualização do Visual Studio para Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="fd941-130">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="fd941-131">Atualmente, o Apple Safari no macOS não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="fd941-131">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="fd941-132">Habilitar depuração</span><span class="sxs-lookup"><span data-stu-id="fd941-132">Enable debugging</span></span>

<span data-ttu-id="fd941-133">Para habilitar a depuração para um :::no-loc(Blazor WebAssembly)::: aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="fd941-133">To enable debugging for an existing :::no-loc(Blazor WebAssembly)::: app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="fd941-134">Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="fd941-134">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="fd941-135">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="fd941-135">The `inspectUri` property:</span></span>

* <span data-ttu-id="fd941-136">Permite que o IDE detecte que o aplicativo é um :::no-loc(Blazor WebAssembly)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-136">Enables the IDE to detect that the app is a :::no-loc(Blazor WebAssembly)::: app.</span></span>
* <span data-ttu-id="fd941-137">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio :::no-loc(Blazor)::: do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-137">Instructs the script debugging infrastructure to connect to the browser through :::no-loc(Blazor):::'s debugging proxy.</span></span>

<span data-ttu-id="fd941-138">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="fd941-138">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fd941-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd941-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fd941-140">Para depurar um :::no-loc(Blazor WebAssembly)::: aplicativo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fd941-140">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span></span>

1. <span data-ttu-id="fd941-141">Crie um novo aplicativo hospedado ASP.NET Core :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="fd941-141">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="fd941-142">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="fd941-142">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fd941-143">Não há suporte para **Iniciar sem depuração** ( <kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="fd941-143">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="fd941-144">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="fd941-144">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="fd941-145">No `*Client*` aplicativo, defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="fd941-145">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="fd941-146">No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fd941-146">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="fd941-147">No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .</span><span class="sxs-lookup"><span data-stu-id="fd941-147">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="fd941-148">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="fd941-148">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="fd941-149">Ao depurar um :::no-loc(Blazor WebAssembly)::: aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="fd941-149">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="fd941-150">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="fd941-150">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="fd941-151">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="fd941-151">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="fd941-152">Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.</span><span class="sxs-lookup"><span data-stu-id="fd941-152">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="fd941-153">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="fd941-153">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="fd941-154">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.</span><span class="sxs-lookup"><span data-stu-id="fd941-154">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="fd941-155">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-155">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fd941-156">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-156">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="fd941-157">Se o aplicativo estiver hospedado em um [caminho base de aplicativo](xref:blazor/host-and-deploy/index#app-base-path) diferente do que o `/` , atualize as seguintes propriedades no `Properties/launchSettings.json` para refletir o caminho base do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="fd941-157">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="fd941-158">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="fd941-158">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="fd941-159">`inspectUri` de cada perfil:</span><span class="sxs-lookup"><span data-stu-id="fd941-159">`inspectUri` of each profile:</span></span>

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

<span data-ttu-id="fd941-160">Os espaços reservados nas configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="fd941-160">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="fd941-161">`{INSECURE PORT}`: A porta não segura.</span><span class="sxs-lookup"><span data-stu-id="fd941-161">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="fd941-162">Um valor aleatório é fornecido por padrão, mas uma porta personalizada é permitida.</span><span class="sxs-lookup"><span data-stu-id="fd941-162">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="fd941-163">`{APP BASE PATH}`: O caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-163">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="fd941-164">`{SECURE PORT}`: A porta segura.</span><span class="sxs-lookup"><span data-stu-id="fd941-164">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="fd941-165">Um valor aleatório é fornecido por padrão, mas uma porta personalizada é permitida.</span><span class="sxs-lookup"><span data-stu-id="fd941-165">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="fd941-166">`{PROFILE 1, 2, ... N}`: Iniciar perfis de configurações.</span><span class="sxs-lookup"><span data-stu-id="fd941-166">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="fd941-167">Normalmente, um aplicativo especifica mais de um perfil por padrão (por exemplo, um perfil para IIS Express e um perfil de projeto, que é usado pelo servidor Kestrel).</span><span class="sxs-lookup"><span data-stu-id="fd941-167">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="fd941-168">Nos exemplos a seguir, o aplicativo é hospedado em `/OAT` com um caminho base de aplicativo configurado em `wwwroot/index.html` como `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="fd941-168">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="fd941-169">Para obter informações sobre como usar um caminho básico do aplicativo personalizado para :::no-loc(Blazor WebAssembly)::: aplicativos, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="fd941-169">For information on using a custom app base path for :::no-loc(Blazor WebAssembly)::: apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fd941-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fd941-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="fd941-171">Depurar autônomo :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="fd941-171">Debug standalone :::no-loc(Blazor WebAssembly):::</span></span></h2>

1. <span data-ttu-id="fd941-172">Abra o :::no-loc(Blazor WebAssembly)::: aplicativo autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="fd941-172">Open the standalone :::no-loc(Blazor WebAssembly)::: app in VS Code.</span></span>

   <span data-ttu-id="fd941-173">Você pode receber uma notificação de que a configuração adicional é necessária para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="fd941-173">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="fd941-174">A configuração adicional é necessária para depurar :::no-loc(Blazor WebAssembly)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fd941-174">Additional setup is required to debug :::no-loc(Blazor WebAssembly)::: applications.</span></span>

   <span data-ttu-id="fd941-175">Se você receber a notificação:</span><span class="sxs-lookup"><span data-stu-id="fd941-175">If you receive the notification:</span></span>

   * <span data-ttu-id="fd941-176">Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada.</span><span class="sxs-lookup"><span data-stu-id="fd941-176">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="fd941-177">Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .</span><span class="sxs-lookup"><span data-stu-id="fd941-177">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="fd941-178">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="fd941-178">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="fd941-179">Abra as configurações na barra de menus (configurações de preferências de **arquivo**  >  **Preferences**  >  **Settings** ).</span><span class="sxs-lookup"><span data-stu-id="fd941-179">Open the settings from the menu bar ( **File** > **Preferences** > **Settings** ).</span></span> <span data-ttu-id="fd941-180">Pesquise usando as palavras-chave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="fd941-180">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="fd941-181">Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada.</span><span class="sxs-lookup"><span data-stu-id="fd941-181">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="fd941-182">Se a opção para habilitar a depuração de visualização não estiver presente, atualize para a versão mais recente do VS Code ou instale a [extensão do depurador do JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versões 1,46 ou anteriores).</span><span class="sxs-lookup"><span data-stu-id="fd941-182">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="fd941-183">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="fd941-183">Reload the window.</span></span>

1. <span data-ttu-id="fd941-184">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="fd941-184">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fd941-185">Não há suporte para **Iniciar sem depuração** ( <kbd>Ctrl</kbd> + <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="fd941-185">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="fd941-186">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="fd941-186">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="fd941-187">Quando solicitado, selecione a opção de **:::no-loc(Blazor WebAssembly)::: depuração** para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-187">When prompted, select the **:::no-loc(Blazor WebAssembly)::: Debug** option to start debugging.</span></span>

1. <span data-ttu-id="fd941-188">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="fd941-188">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="fd941-189">No `*Client*` aplicativo, defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="fd941-189">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="fd941-190">No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fd941-190">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="fd941-191">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-191">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fd941-192">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-192">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="fd941-193">Depuração hospedada :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="fd941-193">Debug hosted :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="fd941-194">Abra a :::no-loc(Blazor WebAssembly)::: pasta de solução do aplicativo hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="fd941-194">Open the hosted :::no-loc(Blazor WebAssembly)::: app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="fd941-195">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="fd941-195">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="fd941-196">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="fd941-196">Select **Yes**.</span></span>

   > <span data-ttu-id="fd941-197">Os ativos necessários para compilar e depurar estão ausentes em ' {nome do aplicativo} '.</span><span class="sxs-lookup"><span data-stu-id="fd941-197">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="fd941-198">Deseja adicioná-los?</span><span class="sxs-lookup"><span data-stu-id="fd941-198">Add them?</span></span>

1. <span data-ttu-id="fd941-199">Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="fd941-199">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="fd941-200">Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="fd941-200">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="fd941-201">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="fd941-201">Attach to an existing debugging session</span></span>

<span data-ttu-id="fd941-202">Para anexar a um aplicativo em execução :::no-loc(Blazor)::: , crie um `launch.json` arquivo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="fd941-202">To attach to a running :::no-loc(Blazor)::: app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> <span data-ttu-id="fd941-203">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="fd941-203">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="fd941-204">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="fd941-204">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="fd941-205">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="fd941-205">Launch configuration options</span></span>

<span data-ttu-id="fd941-206">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="fd941-206">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="fd941-207">Opção</span><span class="sxs-lookup"><span data-stu-id="fd941-207">Option</span></span>    | <span data-ttu-id="fd941-208">Descrição</span><span class="sxs-lookup"><span data-stu-id="fd941-208">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="fd941-209">Use `launch` para iniciar e anexar uma sessão de depuração a um :::no-loc(Blazor WebAssembly)::: aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="fd941-209">Use `launch` to launch and attach a debugging session to a :::no-loc(Blazor WebAssembly)::: app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="fd941-210">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-210">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="fd941-211">Assume o padrão de `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fd941-211">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="fd941-212">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-212">The browser to launch for the debugging session.</span></span> <span data-ttu-id="fd941-213">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="fd941-213">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="fd941-214">Assume o padrão de `chrome`.</span><span class="sxs-lookup"><span data-stu-id="fd941-214">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="fd941-215">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="fd941-215">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="fd941-216">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="fd941-216">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="fd941-217">Deve ser definido como `true` se estiver iniciando e depurando um :::no-loc(Blazor WebAssembly)::: aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="fd941-217">Must be set to `true` if launching and debugging a hosted :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| `webRoot` | <span data-ttu-id="fd941-218">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="fd941-218">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="fd941-219">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="fd941-219">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="fd941-220">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="fd941-220">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="fd941-221">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="fd941-221">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="fd941-222">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="fd941-222">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="fd941-223">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="fd941-223">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="fd941-224">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-224">The working directory to launch the app under.</span></span> <span data-ttu-id="fd941-225">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="fd941-225">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="fd941-226">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="fd941-226">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="fd941-227">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="fd941-227">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="fd941-228">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="fd941-228">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="fd941-229">Iniciar e depurar um :::no-loc(Blazor WebAssembly)::: aplicativo autônomo</span><span class="sxs-lookup"><span data-stu-id="fd941-229">Launch and debug a standalone :::no-loc(Blazor WebAssembly)::: app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="fd941-230">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="fd941-230">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="fd941-231">Iniciar e depurar um :::no-loc(Blazor WebAssembly)::: aplicativo hospedado com o Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fd941-231">Launch and debug a hosted :::no-loc(Blazor WebAssembly)::: app with Microsoft Edge</span></span>

<span data-ttu-id="fd941-232">A configuração do navegador usa como padrão o Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="fd941-232">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="fd941-233">Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` .</span><span class="sxs-lookup"><span data-stu-id="fd941-233">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="fd941-234">Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .</span><span class="sxs-lookup"><span data-stu-id="fd941-234">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="fd941-235">No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="fd941-235">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="fd941-236">A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="fd941-236">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fd941-237">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fd941-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fd941-238">Para depurar um :::no-loc(Blazor WebAssembly)::: aplicativo no Visual Studio para Mac:</span><span class="sxs-lookup"><span data-stu-id="fd941-238">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="fd941-239">Crie um novo aplicativo hospedado ASP.NET Core :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="fd941-239">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="fd941-240">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="fd941-240">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fd941-241">Não há suporte para **Iniciar sem depuração** ( <kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>).</span><span class="sxs-lookup"><span data-stu-id="fd941-241">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="fd941-242">Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.</span><span class="sxs-lookup"><span data-stu-id="fd941-242">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="fd941-243">O Google Chrome ou o Microsoft Edge deve ser o navegador selecionado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-243">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="fd941-244">No `*Client*` aplicativo, defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="fd941-244">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="fd941-245">No navegador, navegue até a `Counter` página e selecione o botão **Click me** para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="fd941-245">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="fd941-246">No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .</span><span class="sxs-lookup"><span data-stu-id="fd941-246">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="fd941-247">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="fd941-247">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="fd941-248">Ao depurar um :::no-loc(Blazor WebAssembly)::: aplicativo, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="fd941-248">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="fd941-249">Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="fd941-249">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="fd941-250">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="fd941-250">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="fd941-251">Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.</span><span class="sxs-lookup"><span data-stu-id="fd941-251">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="fd941-252">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="fd941-252">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="fd941-253">Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.</span><span class="sxs-lookup"><span data-stu-id="fd941-253">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="fd941-254">Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="fd941-254">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="fd941-255">Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-255">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="fd941-256">Para obter mais informações, consulte [Depurando com Visual Studio para Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="fd941-256">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="fd941-257">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="fd941-257">Debug in the browser</span></span>

<span data-ttu-id="fd941-258">*As diretrizes nesta seção aplicam-se ao Google Chrome e ao Microsoft Edge em execução no Windows.*</span><span class="sxs-lookup"><span data-stu-id="fd941-258">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="fd941-259">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="fd941-259">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="fd941-260">Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="fd941-260">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="fd941-261">No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="fd941-261">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="fd941-262">O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão.</span><span class="sxs-lookup"><span data-stu-id="fd941-262">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="fd941-263">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta.</span><span class="sxs-lookup"><span data-stu-id="fd941-263">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="fd941-264">Siga as instruções para seu navegador, que abre uma nova janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="fd941-264">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="fd941-265">Feche a janela anterior do navegador.</span><span class="sxs-lookup"><span data-stu-id="fd941-265">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="fd941-266">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração na etapa anterior abrirá uma nova guia do depurador.</span><span class="sxs-lookup"><span data-stu-id="fd941-266">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="fd941-267">Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.</span><span class="sxs-lookup"><span data-stu-id="fd941-267">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="fd941-268">No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado.</span><span class="sxs-lookup"><span data-stu-id="fd941-268">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="fd941-269">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="fd941-269">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="fd941-270">:::no-loc(Blazor)::: fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="fd941-270">:::no-loc(Blazor)::: provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="fd941-271">Quando o atalho de teclado de depuração é pressionado, :::no-loc(Blazor)::: o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="fd941-271">When debugging keyboard shortcut is pressed, :::no-loc(Blazor)::: points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="fd941-272">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="fd941-272">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="fd941-273">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="fd941-273">Browser source maps</span></span>

<span data-ttu-id="fd941-274">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="fd941-274">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="fd941-275">No entanto, o :::no-loc(Blazor)::: atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="fd941-275">However, :::no-loc(Blazor)::: doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="fd941-276">Em vez disso, a :::no-loc(Blazor)::: interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="fd941-276">Instead, :::no-loc(Blazor)::: does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="fd941-277">Configuração do firewall</span><span class="sxs-lookup"><span data-stu-id="fd941-277">Firewall configuration</span></span>

<span data-ttu-id="fd941-278">Se um firewall bloquear a comunicação com o proxy de depuração, crie uma regra de exceção de firewall que permita a comunicação entre o navegador e o `NodeJS` processo.</span><span class="sxs-lookup"><span data-stu-id="fd941-278">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="fd941-279">A modificação de uma configuração de firewall deve ser feita com cuidado para evitar a criação de vulnerablities de segurança.</span><span class="sxs-lookup"><span data-stu-id="fd941-279">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="fd941-280">Aplique cuidadosamente as diretrizes de segurança, siga as melhores práticas de segurança e respeite os avisos emitidos pelo fabricante do firewall.</span><span class="sxs-lookup"><span data-stu-id="fd941-280">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="fd941-281">Permitindo a comunicação aberta com o `NodeJS` processo:</span><span class="sxs-lookup"><span data-stu-id="fd941-281">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="fd941-282">Abre o servidor de nó para qualquer conexão, dependendo dos recursos e da configuração do firewall.</span><span class="sxs-lookup"><span data-stu-id="fd941-282">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="fd941-283">Pode ser arriscado dependendo da sua rede.</span><span class="sxs-lookup"><span data-stu-id="fd941-283">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="fd941-284">**É recomendado apenas em máquinas de desenvolvedor.**</span><span class="sxs-lookup"><span data-stu-id="fd941-284">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="fd941-285">Se possível, permita apenas a comunicação aberta com o `NodeJS` processo **em redes confiáveis ou privadas**.</span><span class="sxs-lookup"><span data-stu-id="fd941-285">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="fd941-286">Para obter diretrizes de configuração do [Firewall do Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) , consulte [criar um programa de entrada ou regra de serviço](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="fd941-286">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="fd941-287">Para obter mais informações, consulte [Windows Defender firewall com segurança avançada](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) e artigos relacionados no conjunto de documentação do firewall do Windows.</span><span class="sxs-lookup"><span data-stu-id="fd941-287">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fd941-288">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="fd941-288">Troubleshoot</span></span>

<span data-ttu-id="fd941-289">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="fd941-289">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="fd941-290">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="fd941-290">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="fd941-291">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="fd941-291">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="fd941-292">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd941-292">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="fd941-293">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="fd941-293">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="fd941-294">O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**.</span><span class="sxs-lookup"><span data-stu-id="fd941-294">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="fd941-295">Essa é a configuração padrão para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fd941-295">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="fd941-296">Se a depuração não estiver funcionando, confirme se a opção está selecionada.</span><span class="sxs-lookup"><span data-stu-id="fd941-296">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="fd941-297">Se o seu ambiente usa um proxy HTTP, certifique-se de que `localhost` está incluído nas configurações de bypass de proxy.</span><span class="sxs-lookup"><span data-stu-id="fd941-297">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="fd941-298">Isso pode ser feito definindo a `NO_PROXY` variável de ambiente em:</span><span class="sxs-lookup"><span data-stu-id="fd941-298">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="fd941-299">O `launchSettings.json` arquivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="fd941-299">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="fd941-300">No nível de variáveis de ambiente do usuário ou do sistema para que ele se aplique a todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fd941-300">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="fd941-301">Ao usar uma variável de ambiente, reinicie o Visual Studio para que a alteração entre em vigor.</span><span class="sxs-lookup"><span data-stu-id="fd941-301">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="fd941-302">Verifique se os firewalls ou proxies não bloqueiam a comunicação com o proxy de depuração ( `NodeJS` processo).</span><span class="sxs-lookup"><span data-stu-id="fd941-302">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="fd941-303">Para obter mais informações, consulte a seção [configuração do firewall](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="fd941-303">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="fd941-304">Pontos de interrupção em `OnInitialized{Async}` não atingido</span><span class="sxs-lookup"><span data-stu-id="fd941-304">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="fd941-305">O :::no-loc(Blazor)::: proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos.</span><span class="sxs-lookup"><span data-stu-id="fd941-305">The :::no-loc(Blazor)::: framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="fd941-306">É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido.</span><span class="sxs-lookup"><span data-stu-id="fd941-306">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="fd941-307">Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd941-307">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="fd941-308"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="fd941-308"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="fd941-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="fd941-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="fd941-310">Tempo limite do Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="fd941-310">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="fd941-311">Se o Visual Studio lançar uma exceção de que o adaptador de depuração falhou ao ser iniciado mencionando que o tempo limite foi atingido, você poderá ajustar o tempo limite com uma configuração de registro:</span><span class="sxs-lookup"><span data-stu-id="fd941-311">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging ":::no-loc(Blazor):::TimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="fd941-312">O `{TIMEOUT}` espaço reservado no comando anterior é em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="fd941-312">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="fd941-313">Por exemplo, um minuto é atribuído como `60000` .</span><span class="sxs-lookup"><span data-stu-id="fd941-313">For example, one minute is assigned as `60000`.</span></span>
