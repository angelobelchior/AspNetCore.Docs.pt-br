---
title: Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core
author: pranavkm
description: Dicas para aumentar o desempenho em Blazor WebAssembly aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: ea3f197e5bab82f4fb40238fe31cd5ce29ab62ad
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900967"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core

Por [Pranav Krishnamoorthy](https://github.com/pranavkm) e [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly é cuidadosamente projetado e otimizado para permitir alto desempenho na maioria dos cenários de interface do usuário do aplicativo. No entanto, a produção dos melhores resultados depende dos desenvolvedores que usam os padrões e recursos certos. Considere os seguintes aspectos:

* **Taxa de transferência de tempo de execução**: o código .net é executado em um intérprete no tempo de execução do Webassembly, portanto, a taxa de transferência da CPU é limitada Em cenários exigentes, o aplicativo se beneficia da [otimização da velocidade de renderização](#optimize-rendering-speed).
* **Tempo de inicialização**: o aplicativo transfere um tempo de execução do .net para o navegador, portanto, é importante usar recursos que [minimizem o tamanho do download do aplicativo](#minimize-app-download-size).

## <a name="optimize-rendering-speed"></a>Otimizar a velocidade de renderização

As seções a seguir fornecem recomendações para minimizar a carga de trabalho de renderização e melhorar a capacidade de resposta da interface do usuário Seguir esse aviso pode facilmente fazer uma *melhoria de dez vezes ou mais* em velocidades de renderização da interface do usuário.

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Evitar renderização desnecessária de subárvores de componentes

Em tempo de execução, os componentes existem como uma hierarquia. Um componente raiz tem componentes filhos. Por sua vez, os filhos da raiz têm seus próprios componentes filho e assim por diante. Quando ocorre um evento, como um usuário selecionando um botão, é assim Blazor que decide quais componentes serão renderizados novamente:

 1. O evento em si é expedido para qualquer componente que renderizasse o manipulador do evento. Depois de executar o manipulador de eventos, esse componente é renderizado novamente.
 1. Sempre que qualquer componente for rerenderizado, ele fornecerá uma nova cópia dos valores de parâmetro para cada um de seus componentes filho.
 1. Ao receber um novo conjunto de valores de parâmetro, cada componente escolhe se deseja rerenderizar. Por padrão, os componentes são reprocessados se os valores de parâmetro podem ter sido alterados (por exemplo, se forem objetos mutáveis).

As duas últimas etapas dessa sequência continuam recursivamente a hierarquia de componentes. Em muitos casos, toda a subárvore é reprocessada. Isso significa que os eventos que visam componentes de alto nível podem causar processos dispendiosos de reprocessamento porque tudo abaixo desse ponto deve ser reprocessado.

Se você quiser interromper esse processo e evitar a recursão de renderização em uma subárvore específica, poderá:

 * Verifique se todos os parâmetros para um determinado componente são de tipos irmutáveis primitivos (por exemplo,,,, `string` `int` `bool` `DateTime` e outros). A lógica interna para detectar alterações automaticamente ignora a rerenderização se nenhum desses valores de parâmetro foram alterados. Se você renderizar um componente filho com `<Customer CustomerId="@item.CustomerId" />` , em que `CustomerId` é um `int` valor, ele não será renderizado, exceto quando houver `item.CustomerId` alterações.
 * Se você precisar aceitar valores de parâmetro não primitivos, como tipos de modelo personalizados, retornos de chamada de evento ou <xref:Microsoft.AspNetCore.Components.RenderFragment> valores, você poderá substituir <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para controlar a decisão sobre se deve renderizar, que é descrito no [uso da `ShouldRender` ](#use-of-shouldrender) seção.

Ao ignorar a rerenderização de subárvores inteiras, você poderá remover a grande maioria do custo de renderização quando ocorrer um evento.

Talvez você queira fatorar os componentes filho especificamente para que possa ignorar a rerenderização dessa parte da interface do usuário. Essa é uma maneira válida de reduzir o custo de renderização de um componente pai.

#### <a name="use-of-shouldrender"></a>Uso de `ShouldRender`

Se criar um componente somente de interface do usuário que nunca é alterado após a renderização inicial (independentemente de quaisquer valores de parâmetro), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para retornar `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Se o componente requer apenas a rerenderização quando seus valores de parâmetro se modificam de formas específicas, você pode usar campos privados para controlar as informações necessárias para detectar alterações. No exemplo a seguir, `shouldRender` é baseado na verificação de qualquer tipo de alteração ou mutação que deve solicitar uma rerenderização. `prevOutboundFlightId` e `prevInboundFlightId` rastreie informações para a próxima atualização em potencial:

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

No código anterior, um manipulador de eventos também pode ser definido `shouldRender` como para `true` que o componente seja reprocessado após o evento.

Para a maioria dos componentes, esse nível de controle manual não é necessário. Você só deve se preocupar em ignorar as subárvores de renderização se essas subárvores forem particularmente caras de renderizar e estiverem causando atraso na interface do usuário.

Para obter mais informações, consulte <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Virtualização

Ao renderizar grandes quantidades de interface do usuário dentro de um loop, por exemplo, uma lista ou grade com milhares de entradas, a quantidade enorme de operações de renderização pode levar a um retardo na renderização da interface do usuário e, portanto, uma experiência ruim para os usuários. Considerando que o usuário só pode ver um pequeno número de elementos de uma só vez sem rolagem, parece que é dispendioso gastar muito tempo renderizando elementos que não estão visíveis no momento.

Para resolver isso, o Blazor fornece um [ `<Virtualize>` componente](xref:blazor/components/virtualization) interno que cria os comportamentos de aparência e rolagem de uma lista arbitrariamente grande, mas, na verdade, apenas renderiza os itens de lista que estão dentro do visor de rolagem atual. Por exemplo, isso significa que o aplicativo pode ter uma lista com 100.000 entradas, mas apenas pagar o custo de renderização de 20 itens visíveis a qualquer momento. O uso do `<Virtualize>` componente pode escalar verticalmente o desempenho da interface do usuário em ordens de magnitude.

`<Virtualize>` pode ser usado quando:

 * Renderizando um conjunto de itens de dados em um loop.
 * A maioria dos itens não está visível devido à rolagem.
 * Os itens processados são exatamente do mesmo tamanho. Quando o usuário rola para um ponto arbitrário, o componente pode calcular os itens visíveis a serem mostrados.

Veja a seguir um exemplo de uma lista não virtualizada:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Se a `allFlights` coleção mantiver 10.000 itens, ela instancia e renderiza as `<FlightSummary>` instâncias de componente 10.000. Em comparação, o seguinte mostra um exemplo de uma lista virtualizada:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

Embora a interface do usuário resultante tenha a mesma aparência de um usuário, por trás dos bastidores, o componente só instancia e renderiza quantas `<FlightSummary>` instâncias forem necessárias para preencher a região rolável. O conjunto de `<FlightSummary>` instâncias exibidas é recalculado e renderizado conforme o usuário rola.

`<Virtualize>` também tem outros benefícios. Por exemplo, quando um componente solicita dados de uma API externa, `<Virtualize>` permite que o componente busque apenas a fatia de registros que correspondem à região visível atual, em vez de baixar todos os dados da coleção.

Para obter mais informações, consulte <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Crie componentes leves e otimizados

A maioria dos Blazor componentes não exige esforços agressivos de otimização. Isso ocorre porque a maioria dos componentes geralmente não se repete na interface do usuário e não reprocessa em alta frequência. Por exemplo, `@page` componentes e componentes que representam partes de interface de usuário de alto nível, como caixas de diálogo ou formulários, provavelmente aparecem apenas um de cada vez e apenas rerenderizam em resposta a um gesto de usuário. Esses componentes não criam uma carga de trabalho de renderização alta, portanto, você pode usar livremente qualquer combinação de recursos de estrutura que desejar sem se preocupar muito com o desempenho de renderização.

No entanto, também há cenários comuns em que você cria componentes que precisam ser repetidos em escala. Por exemplo:

 * Formulários aninhados grandes podem ter centenas de entradas, rótulos e outros elementos individuais.
 * As grades podem ter milhares de células.
 * As plotagens de dispersão podem ter milhões de pontos de dados.

Se modelar cada unidade como instâncias de componente separadas, haverá muitos deles que o desempenho de renderização se torna crítico. Esta seção fornece conselhos sobre como tornar esses componentes leves para que a interface do usuário permaneça rápida e responsiva.

#### <a name="avoid-thousands-of-component-instances"></a>Evite milhares de instâncias de componentes

Cada componente é uma ilha separada que pode ser renderizada independentemente de seus pais e filhos. Ao escolher como dividir a interface do usuário em uma hierarquia de componentes, você está tomando controle sobre a granularidade da renderização da interface do usuário. Isso pode ser bom ou ruim para o desempenho.

 * Dividindo a interface do usuário em mais componentes, você pode ter partes menores da rerenderização da interface do usuário quando ocorrem eventos. Por exemplo, quando um usuário clica em um botão em uma linha de tabela, você pode ter apenas a rerenderização de linha única em vez de toda a página ou tabela.
 * No entanto, cada componente extra envolve alguma sobrecarga extra de memória e CPU para lidar com seu estado independente e ciclo de vida de renderização.

Ao ajustar o desempenho do Blazor WebAssembly no .NET 5, medimos uma sobrecarga de renderização de cerca de 0, 6 ms por instância de componente. Isso se baseia em um componente simples que aceita três parâmetros em execução em um laptop típico. Internamente, a sobrecarga está em grande parte devido à recuperação do estado por componente de dicionários e passagem e recebimento de parâmetros. Por multiplicação, você pode ver que a adição de 2.000 instâncias de componente extras adicionará 0,12 segundos ao tempo de renderização e a interface do usuário começaria a ficar lenta para os usuários.

É possível tornar os componentes mais leves para que você possa ter mais deles, mas, em geral, a técnica mais poderosa não tem tantos componentes. As seções a seguir descrevem duas abordagens.

##### <a name="inline-child-components-into-their-parents"></a>Componentes filho embutidos em seus pais

Considere o seguinte componente que renderiza uma sequência de componentes filho:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

Para o código de exemplo anterior, o `<ChatMessageDisplay>` componente é definido em um arquivo `ChatMessageDisplay.razor` que contém:

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

O exemplo anterior funciona bem e tem um bom desempenho, desde que milhares de mensagens não sejam mostradas ao mesmo tempo. Para mostrar milhares de mensagens de uma vez, considere *não* fatorar o `ChatMessageDisplay` componente separado. Em vez disso, embutimos a renderização diretamente no pai:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

Isso evita a sobrecarga por componente da renderização de forma que muitos componentes filho tenham o custo de não serem capazes de reprocessar cada um deles de forma independente.

##### <a name="define-reusable-renderfragments-in-code"></a>Definir reutilizável `RenderFragments` no código

Você pode estar decompondo componentes filho puramente como uma maneira de reutilizar a lógica de renderização. Se esse for o caso, ainda é possível reutilizar a lógica de renderização sem declarar os componentes reais. No bloco de qualquer componente `@code` , você pode definir um <xref:Microsoft.AspNetCore.Components.RenderFragment> que emita a interface do usuário e que possa ser chamado de qualquer lugar:

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

Como demonstated no exemplo anterior, os componentes podem emitir marcação a partir do código dentro de seu `@code` bloco e fora dele. Essa abordagem define um <xref:Microsoft.AspNetCore.Components.RenderFragment> delegado que você pode renderizar dentro da saída de renderização normal do componente, opcionalmente em vários lugares. É necessário que o delegado aceite um parâmetro chamado `__builder` do tipo <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para que o Razor compilador possa produzir instruções de renderização para ele.

Se você quiser tornar isso reutilizável em vários componentes, considere declará-lo como um `public static` membro:

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Agora, isso pode ser invocado a partir de um componente não relacionado. Essa técnica é útil para criar bibliotecas de trechos de marcação reutilizáveis que são renderizados sem nenhuma sobrecarga por componente.

<xref:Microsoft.AspNetCore.Components.RenderFragment> os delegados também podem aceitar parâmetros. Para criar o equivalente do `ChatMessageDisplay` componente do exemplo anterior:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

Essa abordagem fornece o benefício de reutilizar a lógica de renderização sem sobrecarga por componente. No entanto, ele não tem o benefício de ser capaz de atualizar sua subárvore da interface do usuário de forma independente, nem tem a capacidade de ignorar o processamento dessa subárvore da interface do usuário quando seu pai é renderizado, já que não há nenhum limite de componente.

#### <a name="dont-receive-too-many-parameters"></a>Não receber muitos parâmetros

Se um componente se repetir de forma extremamente frequente, por exemplo, centenas ou milhares de vezes, lembre-se de que a sobrecarga de passar e receber cada parâmetro se baseia.

É raro que muitos parâmetros restringem seriamente o desempenho, mas pode ser um fator. Para um `<TableCell>` componente que renderiza 1.000 vezes em uma grade, cada parâmetro extra passado para ele poderia adicionar cerca de 15 ms ao custo total de renderização. Se cada célula aceitasse 10 parâmetros, a passagem de parâmetro leva cerca de 150 ms por renderização de componente e, portanto, talvez 150.000 MS (150 segundos) e, por sua própria causa, uma interface de usuário com retardo.

Para reduzir essa carga, você pode reunir vários parâmetros por meio de classes personalizadas. Por exemplo, um `<TableCell>` componente pode aceitar:

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

No exemplo anterior, `Data` é diferente para cada célula, mas `Options` é comum em todos eles. É claro que pode ser uma melhoria não ter um `<TableCell>` componente e, em vez disso, embutir sua lógica no componente pai.

#### <a name="ensure-cascading-parameters-are-fixed"></a>Garantir que os parâmetros em cascata sejam corrigidos

O `<CascadingValue>` componente tem um parâmetro opcional chamado `IsFixed` .

 * Se o `IsFixed` valor for `false` (o padrão), cada destinatário do valor em cascata configurará uma assinatura para receber notificações de alteração. Nesse caso, cada `[CascadingParameter]` um é **substancialmente mais caro** do que `[Parameter]` o normal devido ao acompanhamento da assinatura.
 * Se o `IsFixed` valor for `true` (por exemplo, `<CascadingValue Value="@someValue" IsFixed="true">` ), destinatários receberá o valor inicial, mas *não* configurará nenhuma assinatura para receber atualizações. Nesse caso, cada `[CascadingParameter]` uma é leve e **não mais cara** do que uma normal `[Parameter]` .

Sempre que possível, você deve usar `IsFixed="true"` em valores em cascata. Você pode fazer isso sempre que o valor que está sendo fornecido não for alterado ao longo do tempo. No padrão comum em que um componente passa `this` como um valor em cascata, você deve usar `IsFixed="true"` :

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

Isso faz uma enorme diferença se houver um grande número de outros componentes que recebam o valor em cascata. Para obter mais informações, consulte <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>Evite o atributo nivelamento com `CaptureUnmatchedValues`

Os componentes podem optar por receber valores de parâmetro "não correspondentes" usando o <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> sinalizador:

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Essa abordagem permite passar atributos adicionais arbitrários para o elemento. No entanto, também é muito caro porque o renderizador deve:

* Corresponder a todos os parâmetros fornecidos em relação ao conjunto de parâmetros conhecidos para criar um dicionário.
* Mantenha o controle de como várias cópias do mesmo atributo substituem umas às outras.

Sinta-se à vontade para usar <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> em componentes sem desempenho crítico, como aqueles que não são repetidos com frequência. No entanto, para componentes que são renderizados em escala, como cada item em uma grande lista ou células em uma grade, tente evitar o atributo nivelamento.

Para obter mais informações, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>Implementar `SetParametersAsync` manualmente

Um dos principais aspectos da sobrecarga de renderização por componente é gravar valores de parâmetro de entrada nas `[Parameter]` Propriedades. O renderizador precisa usar a reflexão para fazer isso. Embora isso seja um pouco otimizado, a ausência de suporte JIT no tempo de execução do Webassembly impõe limites.

Em alguns casos extremos, talvez você queira evitar a reflexão e implementar sua própria lógica de configuração de parâmetro manualmente. Isso pode ser aplicável quando:

 * Você tem um componente que renderiza extremamente frequentemente (por exemplo, há centenas ou milhares de cópias dele na interface do usuário).
 * Ele aceita muitos parâmetros.
 * Você descobre que a sobrecarga dos parâmetros de recebimento tem um impacto observável na capacidade de resposta da interface do usuário.

Nesses casos, você pode substituir o método virtual do componente <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e implementar sua própria lógica específica do componente. O exemplo a seguir evita deliberadamente quaisquer pesquisas de dicionário:

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

No código anterior, retornar a classe base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> executa os métodos de ciclo de vida normais sem atribuir parâmetros novamente.

Como você pode ver no código anterior, substituir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e fornecer lógica personalizada é complicado e trabalhoso, portanto, não recomendamos essa abordagem em geral. Em casos extremos, ele pode melhorar o desempenho de renderização em 20-25%, mas você só deve considerar essa abordagem nos cenários listados anteriormente.

### <a name="dont-trigger-events-too-rapidly"></a>Não dispare eventos muito rapidamente

Alguns eventos de navegador são acionados com muita frequência, por exemplo, `onmousemove` e `onscroll` , que podem ser lançados dezenas ou centenas de vezes por segundo. Na maioria dos casos, você não precisa executar atualizações da interface do usuário com frequência. Se você tentar fazer isso, poderá prejudicar a capacidade de resposta da interface do usuário ou consumir tempo excessivo de CPU.

Em vez de usar `@onmousemove` `@onscroll` eventos nativos, você pode preferir usar a interoperabilidade js para registrar um retorno de chamada que é acionado com menos frequência. Por exemplo, o componente a seguir ( `MyComponent.razor` ) exibe a posição do mouse, mas só atualiza no máximo uma vez a cada 500 MS:

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

O código JavaScript correspondente, que pode ser colocado na `index.html` página ou carregado como um módulo ES6, registra o ouvinte de eventos dom real. Neste exemplo, o ouvinte de eventos usa a [ `throttle` função do Lodash](https://lodash.com/docs/4.17.15#throttle) para limitar a taxa de invocações:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

Essa técnica pode ser ainda mais importante para Blazor Server o, já que cada invocação de evento envolve a entrega de uma mensagem pela rede. É valioso Blazor WebAssembly porque pode reduzir muito a quantidade de trabalho de renderização.

## <a name="optimize-javascript-interop-speed"></a>Otimizar a velocidade de interoperabilidade do JavaScript

Chamadas entre .NET e JavaScript envolvem alguma sobrecarga adicional porque:

 * Por padrão, as chamadas são assíncronas.
 * Por padrão, os parâmetros e os valores de retorno são serializados em JSON. Isso é para fornecer um mecanismo de conversão fácil de entender entre os tipos .NET e JavaScript.

Além disso Blazor Server , essas chamadas são passadas pela rede.

### <a name="avoid-excessively-fine-grained-calls"></a>Evite chamadas refinadas excessivamente

Como cada chamada envolve alguma sobrecarga, pode ser importante reduzir o número de chamadas. Considere o código a seguir, que armazena uma coleção de itens na loja do navegador `localStorage` :

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

O exemplo anterior faz uma chamada de interoperabilidade do JS separada para cada item. Em vez disso, a seguinte abordagem reduz a interoperabilidade de JS para uma única chamada:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

A função JavaScript correspondente definida da seguinte maneira:

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

Para Blazor WebAssembly o, isso geralmente só é importante se você estiver fazendo um grande número de chamadas de interoperabilidade js.

### <a name="consider-making-synchronous-calls"></a>Considere fazer chamadas síncronas

As chamadas de interoperabilidade do JavaScript são assíncronas por padrão, independentemente de o código que está sendo chamado ser síncrono ou assíncrono. Isso é para garantir que os componentes sejam compatíveis com o Blazor WebAssembly e o Blazor Server . Em Blazor Server , todas as chamadas de interoperabilidade do JavaScript devem ser assíncronas porque são enviadas por uma conexão de rede.

Se você tiver certeza de que seu aplicativo só é executado Blazor WebAssembly , você pode optar por fazer chamadas de interoperabilidade do JavaScript. Isso tem um pouco menos de sobrecarga do que fazer chamadas assíncronas e pode resultar em menos ciclos de processamento porque não há um estado intermediário ao aguardar os resultados.

Para fazer uma chamada síncrona do .NET para o JavaScript, converta <xref:Microsoft.JSInterop.IJSRuntime> em <xref:Microsoft.JSInterop.IJSInProcessRuntime> :

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

Ao trabalhar com `IJSObjectReference` o, você pode fazer uma chamada síncrona por meio da conversão para `IJSInProcessObjectReference` .

::: moniker-end

Para fazer uma chamada síncrona do JavaScript para o .NET, use `DotNet.invokeMethod` em vez de `DotNet.invokeMethodAsync` .

Chamadas síncronas funcionam se:

* O aplicativo está sendo executado em Blazor WebAssembly , não Blazor Server .
* A função chamada retorna um valor de forma síncrona (não é um `async` método e não retorna um .NET <xref:System.Threading.Tasks.Task> ou JavaScript `Promise` ).

Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Considere fazer chamadas não marshalled

Ao executar em Blazor WebAssembly , é possível fazer chamadas não Marshalling de .net para JavaScript. Essas são chamadas síncronas que não executam a serialização JSON de argumentos ou valores de retorno. Todos os aspectos do gerenciamento de memória e as traduções entre as representações do .NET e do JavaScript são deixados para o desenvolvedor.

> [!WARNING]
> Embora o uso do `IJSUnmarshalledRuntime` tenha a menor sobrecarga das abordagens de interoperabilidade do js, as APIs de JavaScript necessárias para interagir com essas APIs não estão documentadas no momento e estão sujeitas a alterações significativas em versões futuras.

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>Minimizar o tamanho do download do aplicativo

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Corte de IL (linguagem intermediária)

A [corte de assemblies não utilizados de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-trimmer) reduz o tamanho do aplicativo removendo o código não utilizado nos binários do aplicativo. Por padrão, o corte é executado durante a publicação de um aplicativo. Para se beneficiar da remoção, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Vinculação de IL (linguagem intermediária)

A [vinculação de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-linker) reduz o tamanho do aplicativo ao cortar o código não utilizado nos binários do aplicativo. Por padrão, o vinculador de linguagem intermediária (IL) só é habilitado durante a criação da `Release` configuração. Para se beneficiar disso, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Usar System.Text.Jsem

Blazora implementação de interoperabilidade do JS depende de <xref:System.Text.Json> , que é uma biblioteca de SERIALIZAÇÃO JSON de alto desempenho com alocação de memória insuficiente. O uso <xref:System.Text.Json> do não resulta em tamanho de carga de aplicativo adicional ao adicionar uma ou mais bibliotecas JSON alternativas.

Para obter diretrizes de migração, consulte [como migrar do `Newtonsoft.Json` para `System.Text.Json` o ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Assemblies de carga lenta

Carregar assemblies em tempo de execução quando os assemblies são exigidos por uma rota. Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Compactação

Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução. Blazor depende do servidor para executar negotation de conteúdo e fornecer arquivos compactados estaticamente.

Depois que um aplicativo for implantado, verifique se o aplicativo serve arquivos compactados. Inspecione a guia rede no Ferramentas para Desenvolvedores de um navegador e verifique se os arquivos são servidos com `Content-Encoding: br` ou `Content-Encoding: gz` . Se o host não estiver servindo arquivos compactados, siga as instruções em <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Desabilitar recursos não utilizados

Blazor WebAssemblyo tempo de execução do inclui os seguintes recursos do .NET que podem ser desabilitados se o aplicativo não exigir um tamanho de carga menor:

* Um arquivo de dados está incluído para tornar as informações de fuso horário corretas. Se o aplicativo não exigir esse recurso, considere a possibilidade de desabilitá-lo definindo a `BlazorEnableTimeZoneSupport` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Por padrão, Blazor WebAssembly o transporta os recursos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário. Se o aplicativo não exigir localização, você poderá [Configurar o aplicativo para dar suporte à cultura invariável](xref:blazor/globalization-localization), que se baseia na `en-US` cultura:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* As informações de agrupamento estão incluídas para fazer APIs como o <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionamento correto. Se você tiver certeza de que o aplicativo não exige os dados de agrupamento, considere desabilitá-lo definindo a `BlazorWebAssemblyPreserveCollationData` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
