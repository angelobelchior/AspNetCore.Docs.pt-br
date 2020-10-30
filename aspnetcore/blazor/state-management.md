---
title: BlazorGerenciamento de estado ASP.NET Core
author: guardrex
description: Saiba como persistir o estado em Blazor Server aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 1769ddbb95c9ffe373e916c885e411adc3d4c65b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054990"
---
# <a name="aspnet-core-no-locblazor-state-management"></a>BlazorGerenciamento de estado ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)

::: zone pivot="webassembly"

O estado do usuário criado em um Blazor WebAssembly aplicativo é mantido na memória do navegador.

Exemplos de estado do usuário mantido na memória do navegador incluem:

* A hierarquia de instâncias de componente e sua saída de renderização mais recente na interface do usuário renderizada.
* Os valores de campos e propriedades em instâncias de componente.
* Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) .
* Os valores definidos por meio de chamadas de [interoperabilidade do JavaScript](xref:blazor/call-javascript-from-dotnet) .

Quando um usuário fecha e abre novamente o navegador ou recarrega a página, o estado do usuário mantido na memória do navegador é perdido.

## <a name="persist-state-across-browser-sessions"></a>Persistir o estado entre as sessões do navegador

Em geral, mantenha o estado entre as sessões de navegador onde os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.

Para preservar o estado entre as sessões do navegador, o aplicativo deve manter os dados em algum outro local de armazenamento que a memória do navegador. A persistência de estado não é automática. Você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.

A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar. Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:

* Web Forms de várias etapas: é demorado para um usuário reinserir dados para várias etapas concluídas de um formulário da Web de várias etapas se seu estado for perdido. Um usuário perde o estado nesse cenário se ele navega para fora do formulário e retorna mais tarde.
* Carrinhos de compras: qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido. Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.

Um aplicativo só pode persistir o *estado do aplicativo* . As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização. Os componentes e as árvores de renderização geralmente não são serializáveis. Para persistir o estado da interface do usuário, como os nós expandidos de um controle de exibição de árvore, o aplicativo deve usar o código personalizado para modelar o comportamento do estado da interface do usuário como estado do aplicativo serializável.

## <a name="where-to-persist-state"></a>Onde persistir o estado

Existem localizações comuns para o estado persistente:

* [Armazenamento no lado do servidor](#server-side-storage)
* [URL](#url)
* [Armazenamento de navegador](#browser-storage)
* [Serviço de contêiner de estado na memória](#in-memory-state-container-service)

### <a name="server-side-storage"></a>Armazenamento no lado do servidor

Para persistência de dados permanente que abrange vários usuários e dispositivos, o aplicativo pode usar armazenamento independente do lado do servidor acessado por meio de uma API da Web. As opções incluem:

* Armazenamento de blob
* Armazenamento de chave-valor
* Banco de dados relacional
* Armazenamento de tabela

Depois que os dados são salvos, o estado do usuário é mantido e está disponível em qualquer nova sessão do navegador.

Como Blazor WebAssembly os aplicativos são executados inteiramente no navegador do usuário, eles exigem medidas adicionais para acessar sistemas externos seguros, como bancos de dados e serviços de armazenamento. Blazor WebAssembly os aplicativos são protegidos da mesma maneira que os aplicativos de página única (SPAs). Normalmente, um aplicativo autentica um usuário por meio [OAuth](https://oauth.net) / [do OAuth OpenID Connect (OIDC)](https://openid.net/connect/) e, em seguida, interage com os serviços de armazenamento e bancos de dados por meio de chamadas à API da Web para um aplicativo do lado do servidor. O aplicativo do lado do servidor Media a transferência de dados entre o Blazor WebAssembly aplicativo e o serviço ou banco de dados do armazenamento. O Blazor WebAssembly aplicativo mantém uma conexão efêmera com o aplicativo do lado do servidor, enquanto o aplicativo do lado do servidor tem uma conexão persistente com o armazenamento.

Para obter mais informações, consulte os seguintes recursos:

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*Segurança e Identity* los

Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte o seguinte:

* [Bancos de dados do Azure](https://azure.microsoft.com/product-categories/databases/)
* [Documentação do armazenamento do Azure](/azure/storage/)

### <a name="url"></a>URL

Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL. Exemplos de estado de usuário modelados na URL incluem:

* A ID de uma entidade exibida.
* O número da página atual em uma grade paginável.

O conteúdo da barra de endereços do navegador será retido se o usuário recarregar a página manualmente.

Para obter informações sobre como definir padrões de URL com a [`@page`](xref:mvc/views/razor#page) diretiva, consulte <xref:blazor/fundamentals/routing> .

### <a name="browser-storage"></a>Armazenamento de navegador

Para dados transitórios que o usuário está criando ativamente, um local de armazenamento usado normalmente é o navegador [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) e as [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) coleções:

* `localStorage` está no escopo da janela do navegador. Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá. Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias. Os dados persistem `localStorage` até serem explicitamente desmarcados.
* `sessionStorage` está no escopo da guia navegador. Se o usuário recarregar a guia, o estado persiste. Se o usuário fechar a guia ou o navegador, o estado será perdido. Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.

> [!NOTE]
> `localStorage` e `sessionStorage` pode ser usado em Blazor WebAssembly aplicativos, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.

Em geral, `sessionStorage` é mais seguro usar. `sessionStorage` evita o risco de um usuário abrir várias guias e encontrar o seguinte:

* Bugs no armazenamento de estado entre guias.
* Comportamento confuso quando uma guia substitui o estado de outras guias.

`localStorage` é a melhor opção se o aplicativo precisar manter o estado entre fechar e abrir novamente o navegador.

> [!WARNING]
> Os usuários podem exibir ou adulterar os dados armazenados no `localStorage` e no `sessionStorage` .

## <a name="in-memory-state-container-service"></a>Serviço de contêiner de estado na memória

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Salvar o estado do aplicativo antes de uma operação de autenticação](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server é uma estrutura de aplicativo com estado. Na maioria das vezes, o aplicativo mantém uma conexão com o servidor. O estado do usuário é mantido na memória do servidor em um *circuito* . 

Exemplos de estado do usuário mantido em um circuito incluem:

* A hierarquia de instâncias de componente e sua saída de renderização mais recente na interface do usuário renderizada.
* Os valores de campos e propriedades em instâncias de componente.
* Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.

O estado do usuário também pode ser encontrado em variáveis JavaScript no conjunto de memória do navegador por meio de chamadas de [interoperabilidade do JavaScript](xref:blazor/call-javascript-from-dotnet) .

Se um usuário sofrer uma perda de conexão de rede temporária, Blazor o tentará reconectar o usuário ao seu circuito original com seu estado original. No entanto, a reconexão de um usuário ao seu circuito original na memória do servidor nem sempre é possível:

* O servidor não pode manter um circuito desconectado para sempre. O servidor deve liberar um circuito desconectado após um tempo limite ou quando o servidor está sob pressão de memória.
* Em ambientes de implantação com balanceamento de carga e vários servidores, os servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume geral de solicitações. O servidor original solicitações de processamento de um usuário pode ficar indisponível quando o usuário tenta se reconectar.
* O usuário pode fechar e reabrir o navegador ou recarregar a página, o que remove qualquer estado mantido na memória do navegador. Por exemplo, os valores de variáveis JavaScript definidos por meio de chamadas de interoperabilidade JavaScript são perdidos.

Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio. Isso é equivalente a fechar e reabrir um aplicativo de área de trabalho.

## <a name="persist-state-across-circuits"></a>Persistir estado entre circuitos

Em geral, mantenha o estado entre circuitos em que os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.

Para preservar o estado entre circuitos, o aplicativo deve manter os dados em algum outro local de armazenamento que a memória do servidor. A persistência de estado não é automática. Você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.

A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar. Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:

* Web Forms de várias etapas: é demorado para um usuário reinserir dados para várias etapas concluídas de um formulário da Web de várias etapas se seu estado for perdido. Um usuário perde o estado nesse cenário se ele navega para fora do formulário e retorna mais tarde.
* Carrinhos de compras: qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido. Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.

Um aplicativo só pode persistir o *estado do aplicativo* . As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização. Os componentes e as árvores de renderização geralmente não são serializáveis. Para persistir o estado da interface do usuário, como os nós expandidos de um controle de exibição de árvore, o aplicativo deve usar o código personalizado para modelar o comportamento do estado da interface do usuário como estado do aplicativo serializável.

## <a name="where-to-persist-state"></a>Onde persistir o estado

Existem localizações comuns para o estado persistente:

* [Armazenamento no lado do servidor](#server-side-storage)
* [URL](#url)
* [Armazenamento de navegador](#browser-storage)
* [Serviço de contêiner de estado na memória](#in-memory-state-container-service)

### <a name="server-side-storage"></a>Armazenamento no lado do servidor

Para persistência de dados permanente que abrange vários usuários e dispositivos, o aplicativo pode usar o armazenamento do lado do servidor. As opções incluem:

* Armazenamento de blob
* Armazenamento de chave-valor
* Banco de dados relacional
* Armazenamento de tabela

Depois que os dados são salvos, o estado do usuário é mantido e está disponível em qualquer circuito novo.

Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte o seguinte:

* [Bancos de dados do Azure](https://azure.microsoft.com/product-categories/databases/)
* [Documentação do armazenamento do Azure](/azure/storage/)

### <a name="url"></a>URL

Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL. Exemplos de estado de usuário modelados na URL incluem:

* A ID de uma entidade exibida.
* O número da página atual em uma grade paginável.

O conteúdo da barra de endereços do navegador é mantido:

* Se o usuário recarregar a página manualmente.
* Se o servidor Web ficar indisponível e o usuário for forçado a recarregar a página a fim de se conectar a um servidor diferente.

Para obter informações sobre como definir padrões de URL com a [`@page`](xref:mvc/views/razor#page) diretiva, consulte <xref:blazor/fundamentals/routing> .

### <a name="browser-storage"></a>Armazenamento de navegador

Para dados transitórios que o usuário está criando ativamente, um local de armazenamento usado normalmente é o navegador [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) e as [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) coleções:

* `localStorage` está no escopo da janela do navegador. Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá. Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias. Os dados persistem `localStorage` até serem explicitamente desmarcados.
* `sessionStorage` está no escopo da guia navegador. Se o usuário recarregar a guia, o estado persiste. Se o usuário fechar a guia ou o navegador, o estado será perdido. Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.

Em geral, `sessionStorage` é mais seguro usar. `sessionStorage` evita o risco de um usuário abrir várias guias e encontrar o seguinte:

* Bugs no armazenamento de estado entre guias.
* Comportamento confuso quando uma guia substitui o estado de outras guias.

`localStorage` é a melhor opção se o aplicativo precisar manter o estado entre fechar e abrir novamente o navegador.

Advertências para usar o armazenamento de navegador:

* Assim como o uso de um banco de dados do lado do servidor, o carregamento e o salvamento do data são assíncronos.
* Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante o pré-processamento porque a página solicitada não existe no navegador durante o estágio de pré-processamento.
* O armazenamento de alguns kilobytes de dados é razoável para persistir para Blazor Server aplicativos. Além de alguns quilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos na rede.
* Os usuários podem exibir ou adulterar os dados. [ASP.NET Core proteção de dados](xref:security/data-protection/introduction) pode reduzir o risco. Por exemplo, [ASP.NET Core armazenamento de navegador protegido](#aspnet-core-protected-browser-storage) usa a proteção de dados do ASP.NET Core.

Os pacotes NuGet de terceiros fornecem APIs para trabalhar com o `localStorage` e o `sessionStorage` . Vale a pena considerar a escolha de um pacote que use de modo transparente [ASP.NET Core proteção de dados](xref:security/data-protection/introduction). A proteção de dados criptografa os dados armazenados e reduz o risco potencial de adulterar os dados armazenados. Se os dados serializados em JSON forem armazenados em texto sem formatação, os usuários poderão ver os dados usando as ferramentas de desenvolvedor do navegador e também modificar os dados armazenados. Proteger os dados nem sempre é um problema porque os dados podem ser triviais por natureza. Por exemplo, ler ou modificar a cor armazenada de um elemento de interface do usuário não é um risco de segurança significativo para o usuário ou a organização. Evite permitir que os usuários inspecionem ou violem *dados confidenciais* .

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET Core o armazenamento de navegador protegido

ASP.NET Core armazenamento de navegador protegido aproveita a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) para o e o [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!NOTE]
> O armazenamento de navegador protegido depende do ASP.NET Core proteção de dados e tem suporte apenas para Blazor Server aplicativos.

### <a name="save-and-load-data-within-a-component"></a>Salvar e carregar dados dentro de um componente

Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use a [`@inject`](xref:mvc/views/razor#inject) diretiva para injetar uma instância de um dos seguintes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

A escolha depende de qual local de armazenamento do navegador você deseja usar. No exemplo a seguir, `sessionStorage` é usado:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

A `@using` diretiva pode ser colocada no arquivo do aplicativo `_Imports.razor` , em vez de no componente. O uso do `_Imports.razor` arquivo disponibiliza o namespace para segmentos maiores do aplicativo ou todo o aplicativo.

Para persistir o `currentCount` valor no `Counter` componente de um aplicativo com base no Blazor Server modelo de projeto, modifique o `IncrementCount` método a ser usado `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável. Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo. `ProtectedSessionStore` o serializa e desserializa automaticamente os dados JSON para armazenar objetos de estado complexo.

No exemplo de código anterior, os `currentCount` dados são armazenados como `sessionStorage['count']` no navegador do usuário. Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando ASP.NET Core proteção de dados. Os dados criptografados poderão ser inspecionados se `sessionStorage['count']` o for avaliado no console do desenvolvedor do navegador.

Para recuperar os `currentCount` dados, se o usuário retornar ao `Counter` componente posteriormente, incluindo se o usuário está em um novo circuito, use `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua um não `null` resultado <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , não <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é chamado apenas uma vez quando o componente é instanciado pela primeira vez. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página. Para obter mais informações, consulte <xref:blazor/components/lifecycle>.

> [!WARNING]
> Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado. Com o pré-processamento habilitado, um erro é gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.
>
> Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento. Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Manipular o estado de carregamento

Como o armazenamento de navegador é acessado de forma assíncrona em uma conexão de rede, sempre há um período de tempo antes que os dados sejam carregados e disponibilizados para um componente. Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.

Uma abordagem é controlar se os dados são `null` , o que significa que os dados ainda estão sendo carregados. No componente padrão `Counter` , a contagem é mantida em um `int` . [Tornar `currentCount` anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) adicionando um ponto de interrogação ( `?` ) ao tipo ( `int` ):

```csharp
private int? currentCount;
```

Em vez de exibir incondicionalmente a contagem e o **`Increment`** botão, exiba esses elementos somente se os dados forem carregados por meio da verificação <xref:System.Nullable%601.HasValue%2A> :

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Processar pré-processamento

Durante o pré-processamento:

* Não existe uma conexão interativa com o navegador do usuário.
* O navegador ainda não tem uma página na qual possa executar código JavaScript.

`localStorage` ou `sessionStorage` não estão disponíveis durante o pré-processamento. Se o componente tentar interagir com o armazenamento, um erro será gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.

Uma maneira de resolver o erro é desabilitar o pré-processamento. Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador. O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil até `localStorage` ou `sessionStorage` estar disponível.

Para desabilitar o pré-processamento, abra o `Pages/_Host.cshtml` arquivo e altere o `render-mode` atributo do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

O pré-processamento pode ser útil para outras páginas que não usam o `localStorage` ou o `sessionStorage` . Para reter o pré-processamento, adie a operação de carregamento até que o navegador esteja conectado ao circuito. Veja a seguir um exemplo de como armazenar um valor de contador:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Fatorar a preservação do estado para um local comum

Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código. Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado. Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.

No exemplo a seguir de um `CounterStateProvider` componente, os dados do contador são persistidos para `sessionStorage` :

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

O `CounterStateProvider` componente manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.

Para usar o `CounterStateProvider` componente, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador. Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o `CounterStateProvider` componente em volta do <xref:Microsoft.AspNetCore.Components.Routing.Router> no `App` componente ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Os componentes encapsulados recebem e podem modificar o estado do contador persistente. O componente a seguir `Counter` implementa o padrão:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

O componente anterior não precisa interagir com o `ProtectedBrowserStorage` , nem lidar com uma fase de "carregamento".

Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento. Para obter mais informações, consulte a seção [manipular pré-processamento](#handle-prerendering) .

Em geral, o padrão de *componente pai do provedor de estado* é recomendado:

* Para consumir o estado em vários componentes.
* Se houver apenas um objeto de estado de nível superior para persistir.

Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar o estado de persistência globalmente.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Pacote NuGet experimental do armazenamento de navegador protegido

ASP.NET Core armazenamento de navegador protegido aproveita a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) para o e o [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` é um pacote experimental sem suporte não adequado para uso em produção.
>
> O pacote só está disponível para uso em aplicativos ASP.NET Core 3,1 Blazor Server .

### <a name="configuration"></a>Configuração

1. Adicione uma referência de pacote a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .
1. No `Pages/_Host.cshtml` arquivo, adicione o seguinte script dentro da marca de fechamento `</body>` :

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. No `Startup.ConfigureServices` , chame `AddProtectedBrowserStorage` para adicionar `localStorage` e `sessionStorage` serviços à coleção de serviços:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Salvar e carregar dados dentro de um componente

Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use a [`@inject`](xref:mvc/views/razor#inject) diretiva para injetar uma instância de um dos seguintes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

A escolha depende de qual local de armazenamento do navegador você deseja usar. No exemplo a seguir, `sessionStorage` é usado:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

A `@using` instrução pode ser colocada em um `_Imports.razor` arquivo em vez de no componente. O uso do `_Imports.razor` arquivo disponibiliza o namespace para segmentos maiores do aplicativo ou todo o aplicativo.

Para persistir o `currentCount` valor no `Counter` componente de um aplicativo com base no Blazor Server modelo de projeto, modifique o `IncrementCount` método a ser usado `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável. Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo. `ProtectedSessionStore` o serializa e desserializa automaticamente os dados JSON.

No exemplo de código anterior, os `currentCount` dados são armazenados como `sessionStorage['count']` no navegador do usuário. Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando ASP.NET Core proteção de dados. Os dados criptografados poderão ser inspecionados se `sessionStorage['count']` o for avaliado no console do desenvolvedor do navegador.

Para recuperar os `currentCount` dados, se o usuário retornar ao `Counter` componente posteriormente, incluindo se eles estão em um circuito totalmente novo, use `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua o resultado em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , não <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é chamado apenas uma vez quando o componente é instanciado pela primeira vez. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página. Para obter mais informações, consulte <xref:blazor/components/lifecycle>.

> [!WARNING]
> Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado. Com o pré-processamento habilitado, um erro é gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.
>
> Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento. Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Manipular o estado de carregamento

Como o armazenamento de navegador é acessado de forma assíncrona em uma conexão de rede, sempre há um período de tempo antes que os dados sejam carregados e disponibilizados para um componente. Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.

Uma abordagem é controlar se os dados são `null` , o que significa que os dados ainda estão sendo carregados. No componente padrão `Counter` , a contagem é mantida em um `int` . [Tornar `currentCount` anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) adicionando um ponto de interrogação ( `?` ) ao tipo ( `int` ):

```csharp
private int? currentCount;
```

Em vez de exibir incondicionalmente a contagem e o **`Increment`** botão, escolha exibir esses elementos somente se os dados forem carregados:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Processar pré-processamento

Durante o pré-processamento:

* Não existe uma conexão interativa com o navegador do usuário.
* O navegador ainda não tem uma página na qual possa executar código JavaScript.

`localStorage` ou `sessionStorage` não estão disponíveis durante o pré-processamento. Se o componente tentar interagir com o armazenamento, um erro será gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.

Uma maneira de resolver o erro é desabilitar o pré-processamento. Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador. O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil até `localStorage` ou `sessionStorage` estar disponível.

Para desabilitar o pré-processamento, abra o `Pages/_Host.cshtml` arquivo e altere o `render-mode` atributo do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

O pré-processamento pode ser útil para outras páginas que não usam o `localStorage` ou o `sessionStorage` . Para reter o pré-processamento, adie a operação de carregamento até que o navegador esteja conectado ao circuito. Veja a seguir um exemplo de como armazenar um valor de contador:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Fatorar a preservação do estado para um local comum

Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código. Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado. Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.

No exemplo a seguir de um `CounterStateProvider` componente, os dados do contador são persistidos para `sessionStorage` :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

O `CounterStateProvider` componente manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.

Para usar o `CounterStateProvider` componente, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador. Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o `CounterStateProvider` componente em volta do <xref:Microsoft.AspNetCore.Components.Routing.Router> no `App` componente ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Os componentes encapsulados recebem e podem modificar o estado do contador persistente. O componente a seguir `Counter` implementa o padrão:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

O componente anterior não precisa interagir com o `ProtectedBrowserStorage` , nem lidar com uma fase de "carregamento".

Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento. Para obter mais informações, consulte a seção [manipular pré-processamento](#handle-prerendering) .

Em geral, o padrão de *componente pai do provedor de estado* é recomendado:

* Para consumir o estado em vários componentes.
* Se houver apenas um objeto de estado de nível superior para persistir.

Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar o estado de persistência globalmente.

::: moniker-end

## <a name="in-memory-state-container-service"></a>Serviço de contêiner de estado na memória

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
