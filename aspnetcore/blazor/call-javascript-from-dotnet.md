---
title: Chamar funções JavaScript de métodos .NET no ASP.NET Core Blazor
author: guardrex
description: Saiba como invocar funções JavaScript a partir de métodos .NET em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: f5373f1905958ee5c51ee76bd07690d079fb50f5
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570010"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Chamar funções JavaScript de métodos .NET no ASP.NET Core Blazor

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)

Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript. Esses cenários são chamados de *interoperabilidade JavaScript* ( *operabilidade do js* ).

Este artigo aborda a invocação de funções JavaScript do .NET. Para obter informações sobre como chamar métodos .NET do JavaScript, consulte <xref:blazor/call-dotnet-from-javascript> .

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Para chamar o JavaScript do .NET, use a <xref:Microsoft.JSInterop.IJSRuntime> abstração. Para emitir chamadas de interoperabilidade JS, insira a <xref:Microsoft.JSInterop.IJSRuntime> abstração em seu componente. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON. O identificador de função é relativo ao escopo global ( `window` ). Se você quiser chamar `window.someScope.someFunction` , o identificador será `someScope.someFunction` . Não é necessário registrar a função antes que ela seja chamada. O tipo de retorno `T` também deve ser serializável em JSON. `T` deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.

As funções JavaScript que retornam uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) são chamadas com <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> . `InvokeAsync` desenvolve a promessa e retorna o valor esperado pela promessa.

Para Blazor Server aplicativos com pré-processamento habilitado, não é possível chamar o JavaScript durante o pré-processamento inicial. As chamadas de interoperabilidade do JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida. Para obter mais informações, consulte a seção [detectar quando um Blazor Server aplicativo está sendo renderizado](#detect-when-a-blazor-server-app-is-prerendering) .

O exemplo a seguir se baseia em [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) um decodificador baseado em JavaScript. O exemplo demonstra como invocar uma função JavaScript de um método C# que descarrega um requisito do código do desenvolvedor para uma API JavaScript existente. A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto para o componente para exibição.

Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( `.js` ) com uma referência ao arquivo de script:

```html
<script src="exampleJsInterop.js"></script>
```

O seguinte componente:

* Invoca a `convertArray` função JavaScript usando `JS` quando um botão de componente ( **`Convert Array`** ) é selecionado.
* Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres. A cadeia de caracteres é retornada para o componente para exibição.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Para usar a <xref:Microsoft.JSInterop.IJSRuntime> abstração, adote qualquer uma das seguintes abordagens:

* Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração no Razor componente ( `.razor` ):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma `handleTickerChanged` função JavaScript. A função é chamada com <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e não retorna um valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração em uma classe ( `.cs` ):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma `handleTickerChanged` função JavaScript. A função é chamada com `JS.InvokeAsync` e retorna um valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use o `[Inject]` atributo:

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:

* `showPrompt`: Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.
* `displayWelcome`: Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com um `id` de `welcome` .

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Coloque a `<script>` marca que faz referência ao arquivo JavaScript no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

Não coloque uma `<script>` marca em um arquivo de componente porque a `<script>` marca não pode ser atualizada dinamicamente.

Os métodos .NET interoperam com as funções JavaScript no `exampleJsInterop.js` arquivo chamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .

A <xref:Microsoft.JSInterop.IJSRuntime> abstração é assíncrona para permitir Blazor Server cenários. Se o aplicativo for um Blazor WebAssembly aplicativo e você quiser invocar uma função JavaScript de forma síncrona, downcast <xref:Microsoft.JSInterop.IJSInProcessRuntime> e chame <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> em seu lugar. Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Para habilitar o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão, consulte a seção [ Blazor isolamento de JavaScript e referências de objeto](#blazor-javascript-isolation-and-object-references) .

::: moniker-end

O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS. O componente:

* Recebe a entrada do usuário por meio de um prompt do JavaScript.
* Retorna o texto para o componente para processamento.
* Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

1. Quando `TriggerJsPrompt` é executado selecionando o botão do componente **`Trigger JavaScript Prompt`** , a `showPrompt` função JavaScript fornecida no `wwwroot/exampleJsInterop.js` arquivo é chamada.
1. A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente. O componente armazena o nome do usuário em uma variável local, `name` .
1. A cadeia de caracteres armazenada em `name` é incorporada a uma mensagem de boas-vindas, que é passada para uma função JavaScript, `displayWelcome` , que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.

## <a name="call-a-void-javascript-function"></a>Chamar uma função JavaScript void

Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> para o seguinte:

* Funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indefinido](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).
* Se o .NET não for necessário para ler o resultado de uma chamada JavaScript.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Detectar quando um Blazor Server aplicativo está sendo renderizado
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Capturar referências a elementos

Alguns cenários de interoperabilidade JS exigem referências a elementos HTML. Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento, como `focus` ou `play` .

Capture referências a elementos HTML em um componente usando a seguinte abordagem:

* Adicione um `@ref` atributo ao elemento HTML.
* Defina um campo do tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cujo nome corresponde ao valor do `@ref` atributo.

O exemplo a seguir mostra a captura de uma referência ao `username` `<input>` elemento:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com Blazor . Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento. Como o Blazor não interage com o elemento, não há possibilidade de um conflito entre a Blazor representação do elemento e o dom.
>
> No exemplo a seguir, é *perigoso* modificar o conteúdo da lista não ordenada ( `ul` ) porque Blazor interage com o dom para preencher os itens de lista deste elemento ( `<li>` ):
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Se a interoperabilidade do JS converter o conteúdo do elemento `MyList` e Blazor tentar aplicar diffs ao elemento, as diferenças não corresponderão ao dom.

Um <xref:Microsoft.AspNetCore.Components.ElementReference> é passado para o código JavaScript por meio da interoperabilidade do js. O código JavaScript recebe uma `HTMLElement` instância, que pode ser usada com APIs dom normais. Por exemplo, o código a seguir define um método de extensão .NET que permite enviar um clique do mouse para um elemento:

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) em código C# para focalizar um elemento, que é interno da Blazor estrutura e funciona com referências de elemento.

::: moniker-end

Para chamar uma função JavaScript que não retorna um valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . O código a seguir dispara um evento do lado `Click` do cliente chamando a função JavaScript anterior com o capturado <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

Para usar um método de extensão, crie um método de extensão estático que receba a <xref:Microsoft.JSInterop.IJSRuntime> instância:

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

O `clickElement` método é chamado diretamente no objeto. O exemplo a seguir pressupõe que o `TriggerClickEvent` método está disponível no `JsInteropClasses` namespace:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> A `exampleButton` variável é populada apenas depois que o componente é renderizado. Se um não populado <xref:Microsoft.AspNetCore.Components.ElementReference> for passado para o código JavaScript, o código JavaScript receberá um valor de `null` . Para manipular referências de elemento após a conclusão da renderização do componente, use os [ `OnAfterRenderAsync` métodos de ciclo de vida do `OnAfterRender` componente ou](xref:blazor/components/lifecycle#after-component-render).

Ao trabalhar com tipos genéricos e retornar um valor, use <xref:System.Threading.Tasks.ValueTask%601> :

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` é chamado diretamente no objeto com um tipo. O exemplo a seguir pressupõe que o `GenericMethod` está disponível no `JsInteropClasses` namespace:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementos de referência entre componentes

Um <xref:Microsoft.AspNetCore.Components.ElementReference> não pode ser passado entre componentes porque:

* Só é garantido que a instância exista depois que o componente é renderizado, que é durante ou após a execução do método de um componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .
* Um <xref:Microsoft.AspNetCore.Components.ElementReference> é um [`struct`](/csharp/language-reference/builtin-types/struct) , que não pode ser passado como um [parâmetro de componente](xref:blazor/components/index#component-parameters).

Para que um componente pai torne uma referência de elemento disponível para outros componentes, o componente pai pode:

* Permitir que componentes filho registrem retornos de chamada.
* Invoque os retornos de chamada registrados durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento com a referência de elemento passada. Indiretamente, essa abordagem permite que os componentes filho interajam com a referência de elemento do pai.

O exemplo a seguir Blazor WebAssembly ilustra a abordagem.

No `<head>` de `wwwroot/index.html` :

```html
<style>
    .red { color: red }
</style>
```

No `<body>` de `wwwroot/index.html` :

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (componente pai):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

`Shared/SurveyPrompt.razor` (componente filho):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

## <a name="harden-js-interop-calls"></a>Proteger chamadas Interop JS

A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável. Por padrão, um Blazor Server aplicativo expira as chamadas de interoperabilidade js no servidor após um minuto. Se um aplicativo puder tolerar um tempo limite mais agressivo, defina o tempo limite usando uma das seguintes abordagens:

* Globalmente no `Startup.ConfigureServices` , especifique o tempo limite:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Por invocação no código do componente, uma única chamada pode especificar o tempo limite:

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Para obter mais informações sobre esgotamento de recursos, consulte <xref:blazor/security/server/threat-mitigation> .

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitar referências circulares de objeto

Os objetos que contêm referências circulares não podem ser serializados no cliente para:

* Chamadas de método .NET.
* O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.

Para obter mais informações, consulte os seguintes problemas:

* [Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor Isolamento de JavaScript e referências de objeto

Blazor habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão. O isolamento de JavaScript oferece os seguintes benefícios:

* O JavaScript importado não polui mais o namespace global.
* Os consumidores de uma biblioteca e componentes não são necessários para importar o JavaScript relacionado.

Por exemplo, o módulo JavaScript a seguir exporta uma função JavaScript para mostrar um prompt do navegador:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Adicione o módulo JavaScript anterior a uma biblioteca do .NET como um ativo da Web estático ( `wwwroot/exampleJsInterop.js` ) e, em seguida, importe o módulo para o código .NET usando o <xref:Microsoft.JSInterop.IJSRuntime> serviço. O serviço é injetado como `js` (não mostrado) para o exemplo a seguir:

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

O `import` identificador no exemplo anterior é um identificador especial usado especificamente para importar um módulo JavaScript. Especifique o módulo usando seu caminho de ativo da Web estático estável: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` . O espaço reservado `{LIBRARY NAME}` é o nome da biblioteca. O espaço reservado `{PATH UNDER WWWROOT}` é o caminho para o script em `wwwroot` .

<xref:Microsoft.JSInterop.IJSRuntime> importa o módulo como um `IJSObjectReference` , que representa uma referência a um objeto JavaScript do código .net. Use o `IJSObjectReference` para invocar funções JavaScript exportadas do módulo:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` representa uma referência a um objeto JavaScript cujas funções podem ser invocadas de forma síncrona.

`IJSUnmarshalledObjectReference` representa uma referência a um objeto JavaScript cujas funções podem ser invocadas sem a sobrecarga de serializar dados .NET. Isso pode ser usado em Blazor WebAssembly quando o desempenho é crucial:

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

No exemplo anterior, o <xref:Microsoft.JSInterop.IJSRuntime> serviço é injetado na classe e atribuído a `js` (não mostrado).

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Uso de bibliotecas JavaScript que renderizam a interface do usuário (elementos DOM)

Às vezes, você pode querer usar bibliotecas JavaScript que produzem elementos visíveis da interface do usuário dentro do DOM do navegador. À primeira vista, isso pode parecer difícil, pois o Blazor sistema diferencial depende de ter controle sobre a árvore de elementos DOM e é executado em erros se algum código externo modifica a árvore DOM e invalida seu mecanismo para aplicar diffs. Essa não é uma Blazor limitação específica. O mesmo desafio ocorre com qualquer estrutura de interface do usuário baseada em comparação.

Felizmente, é simples inserir a interface do usuário gerada externamente em uma Blazor interface do usuário do componente de forma confiável. A técnica recomendada é fazer com que o código do componente ( `.razor` arquivo) produza um elemento vazio. No que diz Blazor respeito ao sistema diferencial, o elemento está sempre vazio, portanto, o renderizador não recursivamente no elemento e, em vez disso, deixa seu conteúdo sozinho. Isso torna seguro popular o elemento com conteúdo gerenciado de modo externo arbitrário.

O exemplo a seguir demonstra o conceito. Na `if` instrução quando `firstRender` é `true` , faça algo com `myElement` . Por exemplo, chame uma biblioteca JavaScript externa para preenchê-la. Blazor Deixa o conteúdo do elemento sozinho até que este componente seja removido. Quando o componente é removido, a subárvore DOM inteira do componente também é removida.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

Como um exemplo mais detalhado, considere o seguinte componente que renderiza um mapa interativo usando as [APIs de Mapbox de código aberto](https://www.mapbox.com/):

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

O módulo JavaScript correspondente, que deve ser colocado em `wwwroot/mapComponent.js` , é o seguinte:

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

No exemplo anterior, substitua a cadeia de caracteres `{ACCESS TOKEN}` por um token de acesso válido do qual você pode obter https://account.mapbox.com .

Para produzir o estilo correto, adicione a seguinte marca de folha de estilos à página HTML do host ( `index.html` ou `_Host.cshtml` ):

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

O exemplo anterior produz uma interface de usuário de mapa interativa, na qual o usuário:

* Pode arrastar para rolar ou aplicar zoom.
* Clique nos botões para saltar para locais predefinidos.

![Mapa de Mapbox de Tóquio, Japão com botões para selecionar Bristol, Reino Unido e Tóquio, Japão](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Os pontos principais a serem compreendidos são:

 * O `<div>` com o `@ref="mapElement"` é deixado vazio no que diz Blazor respeito. Portanto, é seguro `mapbox-gl.js` preenchê-lo e modificar seu conteúdo ao longo do tempo. Você pode usar essa técnica com qualquer biblioteca JavaScript que renderiza a interface do usuário. Você poderia até mesmo inserir componentes de uma estrutura de SPA do JavaScript de terceiros dentro de Blazor componentes, desde que eles não tentem acessar e modificar outras partes da página. *Não* é seguro que o código JavaScript externo modifique os elementos que não se Blazor consideram como vazios.
 * Ao usar essa abordagem, tenha em mente as regras sobre como o Blazor retém ou destrói elementos DOM. No exemplo anterior, o componente manipula com segurança eventos de clique de botão e atualiza a instância de mapa existente porque os elementos DOM são retidos sempre que possível por padrão. Se você estivesse Renderizando uma lista de elementos de mapa de dentro de um `@foreach` loop, você deseja usar `@key` para garantir a preservação de instâncias de componente. Caso contrário, as alterações nos dados da lista podem fazer com que as instâncias do componente retenham o estado das instâncias anteriores de maneira indesejável. Para obter mais informações, consulte [usando @key para preservar elementos e componentes](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Além disso, o exemplo anterior mostra como é possível encapsular a lógica JavaScript e dependências dentro de um módulo ES6 e carregá-lo dinamicamente usando o `import` identificador. Para obter mais informações, consulte [isolamento de JavaScript e referências de objeto](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>Limites de tamanho em chamadas de interoperabilidade JS

No Blazor WebAssembly , a estrutura não impõe limites para o tamanho das entradas e saídas de chamadas de interoperabilidade do js.

No Blazor Server , o resultado de uma chamada de interoperabilidade js é limitado pelo tamanho máximo de carga imposto por SignalR ( <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> ), que usa como padrão 32 KB. Aplicativos que tentam responder a uma chamada de interoperabilidade JS com uma carga maior do que <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> gerar um erro. Um limite maior pode ser configurado modificando <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> . O exemplo a seguir define o tamanho máximo da mensagem de recebimento como 64 KB (64 * 1024 * 1024):

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

Aumentar o SignalR limite acompanha o custo de exigir o uso de mais recursos de servidor e expõe o servidor para aumentar os riscos de um usuário mal-intencionado. Além disso, a leitura de uma grande quantidade de conteúdo na memória como cadeias de caracteres ou matrizes de bytes também pode resultar em alocações que funcionam mal com o coletor de lixo, resultando em penalidades de desempenho adicionais. Uma opção para ler grandes cargas é considerar o envio do conteúdo em partes menores e o processamento da carga como um <xref:System.IO.Stream> . Isso pode ser usado ao ler cargas JSON grandes ou se os dados estiverem disponíveis em JavaScript como bytes brutos. Para obter um exemplo que demonstra o envio de grandes cargas binárias no Blazor Server que usa técnicas semelhantes ao `InputFile` componente, consulte o [aplicativo de exemplo enviar binário](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

Considere as seguintes diretrizes ao desenvolver código que transfere uma grande quantidade de dados entre o JavaScript e o Blazor :

* Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.
* Não aloque objetos grandes em JavaScript e código C#.
* Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.
* Libere qualquer memória consumida quando o processo for concluído ou cancelado.
* Impor os seguintes requisitos adicionais para fins de segurança:
  * Declare o tamanho máximo de arquivo ou de dados que pode ser passado.
  * Declare a taxa de carregamento mínima do cliente para o servidor.
* Depois que os dados são recebidos pelo servidor, os dados podem ser:
  * Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.
  * Consumido imediatamente. Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.
  
## <a name="js-modules"></a>Módulos JS

Para isolamento de JS, o JS Interop funciona com o suporte padrão do navegador para [módulos ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificação ECMAScript](https://tc39.es/ecma262/#sec-modules)).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/call-dotnet-from-javascript>
* [Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
