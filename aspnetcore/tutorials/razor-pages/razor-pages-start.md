---
title: 'Tutorial: introdução às Razor páginas no ASP.NET Core'
author: rick-anderson
description: Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
- appsettings.json
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360900"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Tutorial: introdução às Razor páginas no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.

Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às Razor páginas](xref:razor-pages/index).

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).

Neste tutorial, você:

> [!div class="checklist"]
> * Create um Razor aplicativo Web de páginas.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho que será aprimorado em Tutoriais posteriores.

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Create um Razor aplicativo Web de páginas

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Inicie o Visual Studio e selecione **Create um novo projeto**. Para obter mais informações, consulte [ Create um novo projeto no Visual Studio](/visualstudio/ide/create-new-project).

   ![::: no-Loc (criar)::: um novo projeto da janela inicial](razor-pages-start/_static/5/start-window-create-new-project.png)

1. Na caixa de diálogo **Create novo projeto** , selecione **ASP.NET Core aplicativo Web** e, em seguida, selecione **Avançar**.

    ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. Na caixa de diálogo **configurar seu novo projeto** , digite `RazorPagesMovie` para **nome do projeto**. É importante nomear o projeto *Razor PagesMovie* , incluindo a correspondência de maiúsculas e minúsculas, de modo que os namespaces corresponderão quando você copiar e colar o código de exemplo.

1. Selecione **Create**.

    ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

1. Na caixa de diálogo **Create novo aplicativo Web ASP.NET Core** , selecione:
    1. **.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.
    1. **Aplicativo Web**.
    1. **Create**.

     ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    O seguinte projeto inicial é criado:

    ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

1. Altere para o diretório (`cd`) que contém o projeto.

1. Execute os seguintes comandos:

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *Razor PagesMovie* .
   * O `code` comando abre a pasta *Razor PagesMovie* na instância atual do Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Selecione **Arquivo** > **Nova Solução**.

    ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**. Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.

    ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. Na caixa de diálogo **Configurar o novo aplicativo Web** :

    1. Confirme se a **autenticação** está definida como **sem autenticação**.
    1. Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão mais recente do .NET 5. x.
    1. Selecione **Avançar**.

1. Nomeie o projeto *Razor PagesMovie* e selecione **Create** .

    ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém Razor páginas e arquivos de suporte. Cada Razor página é um par de arquivos:

* Um arquivo *. cshtml* que tem marcação HTML com código C# usando a Razor sintaxe.
* Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém ativos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### appsettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o aplicativo. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contém código que configura o comportamento do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="next-steps"></a>Próximas etapas

> [!div class="step-by-step"]
> [Em seguida: adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.

Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às Razor páginas](xref:razor-pages/index).

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).

Neste tutorial, você:

> [!div class="checklist"]
> * Create um Razor aplicativo Web de páginas.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Create um Razor aplicativo Web de páginas

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Create um novo aplicativo Web ASP.NET Core e selecione **Avançar**.
  ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nomeie o projeto **Razor PagesMovie**. É importante nomear o projeto *Razor PagesMovie* para que os namespaces correspondam quando você copiar e colar o código.
  ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web** e, em seguida, selecione **Create** .

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere para o diretório (`cd`) que contém o projeto.

* Execute os seguintes comandos:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *Razor PagesMovie* .
  * O `code` comando abre a pasta *Razor PagesMovie* na instância atual do Visual Studio Code.

* Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' Razor PagesMovie '. Adicioná-los?** Selecione **Sim** na barra superior.

  Um diretório *.vscode* , contendo os arquivos *launch.json* e *tasks.json* , é adicionado ao diretório raiz do projeto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**. Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* Na caixa de diálogo **Configurar o novo aplicativo Web** :

  * Confirme se a **autenticação** está definida como **sem autenticação**.
  * Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 3. x mais recente.

  Selecione **Avançar**.

* Nomeie o projeto **Razor PagesMovie** e, em seguida, selecione **Create** .

  ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém Razor páginas e arquivos de suporte. Cada Razor página é um par de arquivos:

* Um arquivo *. cshtml* que tem marcação HTML com código C# usando a Razor sintaxe.
* Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contém código que configura o comportamento do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="next-steps"></a>Próximas etapas

> [!div class="step-by-step"]
> [Em seguida: adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Este é o primeiro tutorial de uma série. [A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um Razor aplicativo Web de ASP.NET Core páginas.

Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às Razor páginas](xref:razor-pages/index).

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).

Neste tutorial, você:

> [!div class="checklist"]
> * Create um Razor aplicativo Web de páginas.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Create um Razor aplicativo Web de páginas

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.

* Create um novo aplicativo Web ASP.NET Core e selecione **Avançar**.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nomeie o projeto **Razor PagesMovie**. É importante nomear o projeto *Razor PagesMovie* para que os namespaces correspondam quando você copiar e colar o código.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selecione **ASP.NET Core 2,2** no menu suspenso, **aplicativo Web** e, em seguida, selecione **Create** .

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere para o diretório (`cd`) que contém o projeto.

* Execute os seguintes comandos:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *Razor PagesMovie* .
  * O `code` comando abre a pasta *Razor PagesMovie* na instância atual do Visual Studio Code.

* Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' Razor PagesMovie '. Adicioná-los?** Selecione **Sim** na barra superior.

  Um diretório *.vscode* , contendo os arquivos *launch.json* e *tasks.json* , é adicionado ao diretório raiz do projeto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**. Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.

* Na caixa de diálogo **Configurar o novo aplicativo Web** :

  * Confirme se a **autenticação** está definida como **sem autenticação**.
  * Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 2. x mais recente.

  Selecione **Avançar**.

* Nomeie o projeto **Razor PagesMovie** e, em seguida, selecione **Create** .

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione Ctrl + F5 para execução sem o depurador.

  Iniciar o aplicativo com <kbd>Ctrl + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.


  [!INCLUDE[](~/includes/trustCertVS.md)]

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo após o consentimento para acompanhamento é fornecido:

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Pressione <kbd>Ctrl + F5</kbd> para executar sem o depurador.

  Iniciar o aplicativo com <kbd>Ctrl + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.

  Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e vai para `http://localhost:5001` . A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Pressione **Cmd-Opt-F5** para execução sem o depurador.

  Iniciar o aplicativo com <kbd>cmd + opt + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.

  O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e vai para `http://localhost:5001` .

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  A imagem a seguir mostra o aplicativo após o consentimento para acompanhamento é fornecido:

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém Razor páginas e arquivos de suporte. Cada Razor página é um par de arquivos:

* Um arquivo *. cshtml* que tem marcação HTML com código C# usando a Razor sintaxe.
* Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

Razor As páginas são derivadas de `PageModel` . Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contém o código que configura o comportamento do aplicativo, como se ele requer consentimento para cookie s. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Próximas etapas

> [!div class="step-by-step"]
> [Em seguida: adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end
