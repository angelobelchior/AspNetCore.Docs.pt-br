---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Explore ASP.NET Core Blazor , uma maneira de criar interface do usuário da Web interativa do lado do cliente com o .net em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 7ad374fdc7452664e3367d6ef214fd4cebdf3b08
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805512"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>Introdução ao ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

*Bem-vindo ao Blazor !*

Blazor é uma estrutura para a criação de interface do usuário da Web interativa do lado do cliente com o [.net](/dotnet/standard/tour):

* Crie interfaces de uso avançadas e interativas usando [C#](/dotnet/csharp/) em vez de [JavaScript](https://www.javascript.com).
* Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.
* Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.
* Integre-se com plataformas de hospedagem modernas, como o [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:

* escreva o código em C# em vez de JavaScript.
* Aproveite o ecossistema .NET existente das [bibliotecas .net](/dotnet/standard/class-libraries).
* Compartilhe a lógica de aplicativo entre o servidor e o cliente.
* Beneficie-se com o desempenho, confiabilidade e segurança do .NET.
* Mantenha-se produtivo com o [Visual Studio](https://visualstudio.microsoft.com) no Windows, no Linux e no MacOS.
* Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.

## <a name="components"></a>Componentes

Blazor os aplicativos são baseados em *componentes*. Um componente no Blazor é um elemento da interface do usuário, como um formulário de página, caixa de diálogo ou entrada de dados.

Os componentes são classes .NET C# embutidas em [assemblies .net](/dotnet/standard/assembly/) que:

* Definem a lógica de renderização da interface de usuário flexível.
* Tratam eventos do usuário.
* Podem ser aninhados e reutilizados.
* Pode ser compartilhado e distribuído como [ Razor bibliotecas de classes](xref:razor-pages/ui-class) ou [pacotes NuGet](/nuget/what-is-nuget).

A classe de componente geralmente é escrita na forma de uma [Razor](xref:mvc/views/razor) página de marcação com uma `.razor` extensão de arquivo. Os componentes no Blazor são formalmente chamados de * Razor componentes*. Razor é uma sintaxe para combinar marcação HTML com código C# projetado para a produtividade do desenvolvedor. Razor permite que você alterne entre marcação HTML e C# no mesmo arquivo com suporte à programação [IntelliSense](/visualstudio/ide/using-intellisense) no Visual Studio. Razor As páginas e MVC também usam o Razor . Ao contrário de Razor páginas e MVC, que são criadas em um modelo de solicitação/resposta, os componentes são usados especificamente para a lógica e a composição da interface do usuário do lado do cliente.

Blazor usa marcas HTML naturais para composição da interface do usuário. A marcação a seguir Razor demonstra um componente ( `Dialog.razor` ) que exibe uma caixa de diálogo e processa um evento quando o usuário seleciona um botão:

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

No exemplo anterior, `OnYes` é um método C# disparado pelo evento do botão `onclick` . O texto () e o título () da caixa de diálogo `ChildContent` `Title` são fornecidos pelo seguinte componente que usa esse componente em sua interface do usuário.

O `Dialog` componente é aninhado dentro de outro componente usando uma marca HTML. No exemplo a seguir, o `Index` componente ( `Pages/Index.razor` ) usa o `Dialog` componente anterior. O atributo da marca `Title` passa um valor para o título para a `Dialog` Propriedade do componente `Title` .  O `Dialog` texto do componente ( `ChildContent` ) é definido pelo conteúdo do `<Dialog>` elemento. Quando o `Dialog` componente é adicionado ao `Index` componente, o [IntelliSense no Visual Studio](/visualstudio/ide/using-intellisense) agiliza o desenvolvimento com a conclusão da sintaxe e do parâmetro.

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

A caixa de diálogo é renderizada quando o `Index` componente é acessado em um navegador. Quando o botão é selecionado pelo usuário, o console de ferramentas de desenvolvedor do navegador mostra a mensagem escrita pelo `OnYes` método:

![Componente de caixa de diálogo renderizado no navegador aninhado dentro do componente de índice. O console de ferramentas de desenvolvedor do navegador mostra a mensagem escrita pelo código C# quando o usuário seleciona Sim! na interface do usuário.](index/_static/dialog.png)

Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento do navegador [(dom)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) chamado de *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.

## Blazor WebAssembly

Blazor WebAssembly o é uma [estrutura de Spa (aplicativo de página única)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) para a criação de aplicativos Web do lado do cliente interativos com o .net. Blazor WebAssembly usa padrões abertos da Web sem plugins ou recompilando código em outras linguagens. Blazor WebAssembly funciona em todos os navegadores da Web modernos, incluindo navegadores móveis.

A execução de código .NET dentro de navegadores da Web é possibilitada pelo [Webassembly](https://webassembly.org) (abreviado `wasm` ). O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução. O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.

O código Webassembly pode acessar a funcionalidade completa do navegador via JavaScript, chamada de *interoperabilidade de JavaScript*, geralmente reduzida para interoperabilidade de *JavaScript* ou de *js*. O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.

![::: no-Loc (Webassembly do mais alto)::: executa o código .NET no navegador com Webassembly.](index/_static/blazor-webassembly.png)

Quando um Blazor WebAssembly aplicativo é compilado e executado em um navegador:

* Arquivos de código C# e Razor arquivos são compilados em assemblies .net.
* Os assemblies e o [tempo de execução do .net](/dotnet/framework/get-started/overview) são baixados para o navegador.
* Blazor WebAssembly Inicializa o tempo de execução do .NET e configura o tempo de execução para carregar os assemblies para o aplicativo. O Blazor WebAssembly tempo de execução usa a interoperabilidade JavaScript para lidar com a manipulação de Dom e chamadas de API de navegador

O tamanho do aplicativo publicado, seu *tamanho de payload*, é um fator de desempenho crítico para a utilidade do aplicativo. Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário. Blazor WebAssembly otimiza o tamanho da carga para reduzir os tempos de download:

::: moniker range=">= aspnetcore-5.0"

* O código não utilizado é removido do aplicativo quando é publicado pela [corte de Il (linguagem intermediária)](xref:blazor/host-and-deploy/configure-trimmer).
* As respostas HTTP são compactadas.
* O runtime do .NET e os assemblies são armazenados em cache no navegador.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:blazor/host-and-deploy/configure-linker).
* As respostas HTTP são compactadas.
* O runtime do .NET e os assemblies são armazenados em cache no navegador.

::: moniker-end

## Blazor Server

Blazor dissocia a lógica de renderização do componente de como as atualizações da interface do usuário são aplicadas. *Blazor Server* fornece suporte para hospedar Razor componentes no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário são manipuladas em uma [SignalR](xref:signalr/introduction) conexão.

Os identificadores de tempo de execução:

* Enviando eventos de interface do usuário do navegador para o servidor.
* Aplicando atualizações de interface do usuário ao componente renderizado que são enviados de volta pelo servidor.

A conexão usada pelo Blazor Server para se comunicar com o navegador também é usada para lidar com as chamadas de interoperabilidade do JavaScript.

![::: no-Loc (servidor de maior número)::: executa o código .NET no servidor e interage com o Modelo de Objeto do Documento no cliente sobre uma::: no-Loc (Signalr)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilidade do JavaScript

Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript. Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar. O código c# pode chamar o código [JavaScript](xref:blazor/call-javascript-from-dotnet)e o código JavaScript pode [chamar código C#](xref:blazor/call-dotnet-from-javascript).

## <a name="code-sharing-and-net-standard"></a>Compartilhamento de código e o .NET Standard

Blazor implementa o [.net Standard](/dotnet/standard/net-standard), que permite que os Blazor projetos referenciem bibliotecas que estão em conformidade com .net Standard especificações. O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET. .NET Standard bibliotecas de classe podem ser compartilhadas entre diferentes plataformas .NET, como Blazor , .NET Framework, .NET Core, Xamarin, mono e Unity.

As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Recursos adicionais

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [Guia do C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Incrível Blazor ](https://github.com/AdrienTorris/awesome-blazor) links da Comunidade
