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
ms.openlocfilehash: d4fd0d501ff14e37bb55b78bb6493ad43f9e5a87
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805564"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core de depuração Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly os aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome). Você também pode depurar seu aplicativo usando os seguintes IDEs (ambientes de desenvolvimento integrado):

* Visual Studio
* Visual Studio para Mac
* Visual Studio Code

Os cenários disponíveis incluem:

* Definir e remover pontos de interrupção.
* Execute o aplicativo com suporte para depuração em IDEs.
* Etapa única pelo código.
* Retome a execução de código com um atalho de teclado em IDEs.
* Na janela *locais* , observe os valores de variáveis locais.
* Consulte a pilha de chamadas, incluindo cadeias de chamada entre JavaScript e .NET.

Por enquanto, você *não pode*:

* Interromper em exceções sem tratamento.
* Acerte os pontos de interrupção durante a inicialização do aplicativo antes da execução do proxy de depuração. Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

A depuração requer um dos seguintes navegadores:

* Google Chrome (versão 70 ou posterior) (padrão)
* Microsoft Edge (versão 80 ou posterior)

Visual Studio para Mac requer a versão 8,8 (Build 1532) ou posterior:

1. Instale a versão mais recente do Visual Studio para Mac selecionando o botão **baixar Visual Studio para Mac** na [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).
1. Selecione o canal de *Visualização* de dentro do Visual Studio. Para obter mais informações, consulte [instalar uma versão de visualização do Visual Studio para Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> Atualmente, o Apple Safari no macOS não tem suporte.

## <a name="enable-debugging"></a>Habilitar depuração

Para habilitar a depuração para um Blazor WebAssembly aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

A `inspectUri` Propriedade:

* Permite que o IDE detecte que o aplicativo é um Blazor WebAssembly aplicativo.
* Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.

Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para depurar um Blazor WebAssembly aplicativo no Visual Studio:

1. Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .
1. Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.

   > [!NOTE]
   > Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>). Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.

1. No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .
1. No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.
1. No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .
1. Pressione <kbd>F5</kbd> para continuar a execução.

Ao depurar um Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:

1. Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.
1. Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.
1. Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .
1. Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.

> [!NOTE]
> Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração. Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.

Se o aplicativo estiver hospedado em um [caminho base de aplicativo](xref:blazor/host-and-deploy/index#app-base-path) diferente do que o `/` , atualize as seguintes propriedades no `Properties/launchSettings.json` para refletir o caminho base do aplicativo:

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` de cada perfil:

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

Os espaços reservados nas configurações anteriores:

* `{INSECURE PORT}`: A porta não segura. Um valor aleatório é fornecido por padrão, mas uma porta personalizada é permitida.
* `{APP BASE PATH}`: O caminho base do aplicativo.
* `{SECURE PORT}`: A porta segura. Um valor aleatório é fornecido por padrão, mas uma porta personalizada é permitida.
* `{PROFILE 1, 2, ... N}`: Iniciar perfis de configurações. Normalmente, um aplicativo especifica mais de um perfil por padrão (por exemplo, um perfil para IIS Express e um perfil de projeto, que é usado pelo servidor Kestrel).

Nos exemplos a seguir, o aplicativo é hospedado em `/OAT` com um caminho base de aplicativo configurado em `wwwroot/index.html` como `<base href="/OAT/">` :

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Para obter informações sobre como usar um caminho básico do aplicativo personalizado para Blazor WebAssembly aplicativos, consulte <xref:blazor/host-and-deploy/index#app-base-path> .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Depurar autônomo Blazor WebAssembly</h2>

1. Abra o Blazor WebAssembly aplicativo autônomo no vs Code.

   Você pode receber uma notificação de que a configuração adicional é necessária para habilitar a depuração:

   > A configuração adicional é necessária para depurar Blazor WebAssembly aplicativos.

   Se você receber a notificação:

   * Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada. Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .
   * Confirme se a depuração de visualização do JavaScript está habilitada. Abra as configurações na barra de menus (configurações de preferências de**arquivo**  >  **Preferences**  >  **Settings**). Pesquise usando as palavras-chave `debug preview` . Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada. Se a opção para habilitar a depuração de visualização não estiver presente, atualize para a versão mais recente do VS Code ou instale a [extensão do depurador do JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versões 1,46 ou anteriores).
   * Recarregue a janela.

1. Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.

   > [!NOTE]
   > Não há suporte para **Iniciar sem depuração** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>). Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.

1. Quando solicitado, selecione a opção de ** Blazor WebAssembly depuração** para iniciar a depuração.

1. O aplicativo autônomo é iniciado e um navegador de depuração é aberto.

1. No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .

1. No navegador, navegue até a `Counter` página e selecione o botão **clique em mim** para atingir o ponto de interrupção.

> [!NOTE]
> Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração. Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Depuração hospedada Blazor WebAssembly

1. Abra a Blazor WebAssembly pasta de solução do aplicativo hospedado no vs Code.

1. Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida. Selecione **Sim** na barra superior.

   > Os ativos necessários para compilar e depurar estão ausentes em ' {nome do aplicativo} '. Deseja adicioná-los?

1. Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.

Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.

## <a name="attach-to-an-existing-debugging-session"></a>Anexar a uma sessão de depuração existente

Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos. Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.

## <a name="launch-configuration-options"></a>Opções de configuração de inicialização

As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).

| Opção    | Descrição |
| --------- | ----------- |
| `request` | Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor WebAssembly aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução. |
| `url`     | A URL a ser aberta no navegador durante a depuração. Assume o padrão de `https://localhost:5001`. |
| `browser` | O navegador a ser iniciado para a sessão de depuração. Definir como `edge` ou `chrome`. Assume o padrão de `chrome`. |
| `trace`   | Usado para gerar logs do depurador JS. Defina como `true` para gerar logs. |
| `hosted`  | Deve ser definido como `true` se estiver iniciando e depurando um Blazor WebAssembly aplicativo hospedado. |
| `webRoot` | Especifica o caminho absoluto do servidor Web. Deve ser definido se um aplicativo for servido de uma sub-roteiro. |
| `timeout` | O número de milissegundos a aguardar a sessão de depuração ser anexada. O padrão é 30.000 milissegundos (30 segundos). |
| `program` | Uma referência ao executável para executar o servidor do aplicativo hospedado. Deve ser definido se `hosted` for `true` . |
| `cwd`     | O diretório de trabalho para o qual iniciar o aplicativo. Deve ser definido se `hosted` for `true` . |
| `env`     | As variáveis de ambiente a serem fornecidas ao processo iniciado. Aplicável somente se `hosted` for definido como `true` . |

## <a name="example-launch-configurations"></a>Exemplo de configurações de inicialização

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Iniciar e depurar um Blazor WebAssembly aplicativo autônomo

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Anexar a um aplicativo em execução em uma URL especificada

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Iniciar e depurar um Blazor WebAssembly aplicativo hospedado com o Microsoft Edge

A configuração do navegador usa como padrão o Google Chrome. Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` . Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .

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

No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* . A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Para depurar um Blazor WebAssembly aplicativo no Visual Studio para Mac:

1. Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .
1. Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para executar o aplicativo no depurador.

   > [!NOTE]
   > Não há suporte para **Iniciar sem depuração** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>). Quando o aplicativo é executado na configuração de depuração, a sobrecarga de depuração sempre resulta em uma pequena redução de desempenho.

   > [!IMPORTANT]
   > O Google Chrome ou o Microsoft Edge deve ser o navegador selecionado para a sessão de depuração.

1. No aplicativo *cliente* , defina um ponto de interrupção na `currentCount++;` linha em `Pages/Counter.razor` .
1. No navegador, navegue até a `Counter` página e selecione o botão **Click me** para atingir o ponto de interrupção:
1. No Visual Studio, inspecione o valor do `currentCount` campo na janela **locais** .
1. Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução.

Ao depurar um Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:

1. Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.
1. Navegue até a `Fetch Data` página para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor.
1. Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` .
1. Pressione <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada no navegador.

> [!NOTE]
> Os pontos de interrupção **não** são atingidos durante a inicialização do aplicativo antes da execução do proxy de depuração. Isso inclui pontos de interrupção em `Program.Main` ( `Program.cs` ) e pontos de interrupção nos [ `OnInitialized{Async}` métodos](xref:blazor/components/lifecycle#component-initialization-methods) de componentes que são carregados pela primeira página solicitada do aplicativo.

Para obter mais informações, consulte [Depurando com Visual Studio para Mac](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Depurar no navegador

*As diretrizes nesta seção aplicam-se ao Google Chrome e ao Microsoft Edge em execução no Windows.*

1. Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.

1. Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).

1. No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.

   O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão. Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta. Siga as instruções para seu navegador, que abre uma nova janela do navegador. Feche a janela anterior do navegador.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração na etapa anterior abrirá uma nova guia do depurador.

1. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.

1. No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado. Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.

Blazor fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede. Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy. O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).

## <a name="browser-source-maps"></a>Mapas de origem do navegador

Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente. No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM. Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.

## <a name="troubleshoot"></a>Solucionar problemas

Se você estiver executando erros, as dicas a seguir podem ajudar:

* Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador. No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.
* Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS. Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**. Essa é a configuração padrão para o Visual Studio. Se a depuração não estiver funcionando, confirme se a opção está selecionada.
* Se o seu ambiente usa um proxy HTTP, certifique-se de que `localhost` está incluído nas configurações de bypass de proxy. Isso pode ser feito definindo a `NO_PROXY` variável de ambiente em:
  * O `launchSettings.json` arquivo para o projeto.
  * No nível de variáveis de ambiente do usuário ou do sistema para que ele se aplique a todos os aplicativos. Ao usar uma variável de ambiente, reinicie o Visual Studio para que a alteração entre em vigor.

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Pontos de interrupção em `OnInitialized{Async}` não atingido

O Blazor proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos. É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido. Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Tempo limite do Visual Studio (Windows)

Se o Visual Studio lançar uma exceção de que o adaptador de depuração falhou ao ser iniciado mencionando que o tempo limite foi atingido, você poderá ajustar o tempo limite com uma configuração de registro:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

O `{TIMEOUT}` espaço reservado no comando anterior é em milissegundos. Por exemplo, um minuto é atribuído como `60000` .
