---
title: Componentes de teste no ASP.NET Core Blazor
author: guardrex
description: Saiba como testar o componments em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 30c5ead98c5da934c1e76577c5dc1a39c7224a79
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253713"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>Componentes de teste no ASP.NET Core Blazor

[Egil Hansen](https://egilhansen.com/)

O teste é um aspecto importante da criação de software estável e passível de manutenção.

Para testar um Blazor componente, o *componente em teste* (recortar) é:

* Renderizado com a entrada relevante para o teste.
* Dependendo do tipo de teste executado, possivelmente sujeito à interação ou modificação. Por exemplo, os manipuladores de eventos podem ser disparados, como um `onclick` evento para um botão.
* Inspecionado os valores esperados.

## <a name="test-approaches"></a>Abordagens de teste

Duas abordagens comuns para testar Blazor componentes são testes de E2E (ponta a ponta) e testes de unidade:

* **Teste de unidade**: os [testes de unidade](/dotnet/core/testing/) são gravados com uma biblioteca de testes de unidade que fornece:
  * Renderização de componente.
  * Inspeção da saída e do estado do componente.
  * Disparo de manipuladores de eventos e métodos de ciclo de vida.
  * Asserções de que o comportamento do componente está correto.

  [bUnit](https://github.com/egil/bUnit) é um exemplo de uma biblioteca que habilita o Razor teste de unidade de componente.

* **Teste de E2E**: um executor de teste executa um Blazor aplicativo que contém o recorte e automatiza uma instância do navegador. A ferramenta de teste inspeciona e interage com o corte através do navegador. [Selenium](https://github.com/SeleniumHQ/selenium) é um exemplo de uma estrutura de teste do e2e que pode ser usada com Blazor aplicativos.

No teste de unidade, apenas o Blazor componente ( Razor /c #) está envolvido. Dependências externas, como os serviços e a interoperabilidade JS, devem ser simuladas. No teste do E2E, o Blazor componente e toda a sua infraestrutura auxiliar fazem parte do teste, incluindo CSS, js e APIs de navegador e dom.

*Escopo de teste* descreve a extensão dos testes. O escopo de teste normalmente tem uma influência sobre a velocidade dos testes. Os testes de unidade são executados em um subconjunto dos subsistemas do aplicativo e geralmente são executados em milissegundos. Os testes de E2E, que testam um grupo amplo de subsistemas do aplicativo, podem levar vários segundos para serem concluídos. 

O teste de unidade também fornece acesso à instância do recorte, permitindo a inspeção e a verificação do estado interno do componente. Isso normalmente não é possível no teste de E2E.

Com relação ao ambiente do componente, os testes de E2E devem garantir que o estado ambiental esperado tenha sido atingido antes do início da verificação. Caso contrário, o resultado será imprevisível. No teste de unidade, o processamento do recorte e do ciclo de vida do teste são mais integrados, o que melhora a estabilidade do teste.

Os testes de E2E envolvem a inicialização de vários processos, a rede e a e/s de disco e outras atividades de subsistema que geralmente levam a uma confiabilidade de teste ruim. Os testes de unidade normalmente são isolados desses tipos de problemas.

A tabela a seguir resume a diferença entre as duas abordagens de teste.

| Recurso                       | Teste de unidade                     | Teste de E2E                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Escopo do teste                       | Blazor componente ( Razor apenas/c #) | Blazor componente ( Razor /c #) com CSS/JS |
| Tempo de execução do teste              | Milissegundos                     | Segundos                                 |
| Acesso à instância do componente | Sim                              | Não                                      |
| Sensível ao ambiente     | Não                               | Sim                                     |
| Confiabilidade                      | Mais confiável                    | Menos confiável                           |

## <a name="choose-the-most-appropriate-test-approach"></a>Escolha a abordagem de teste mais apropriada

Considere o cenário ao escolher o tipo de teste a ser executado. Algumas considerações são descritas na tabela a seguir.

| Cenário | Abordagem sugerida | Comentários |
| -------- | ------------------ | ------- |
| Componente sem lógica de interoperabilidade do JS | Teste de unidade | Quando não há nenhuma dependência na interoperabilidade do JS em um Blazor componente, o componente pode ser testado sem acesso ao js ou à API do dom. Nesse cenário, não há desvantagens em escolher o teste de unidade. |
| Componente com lógica de interoperabilidade simples do JS | Teste de unidade | É comum que os componentes consultem o DOM ou disparem animações por meio da interoperabilidade JS. Em geral, o teste de unidade é preferido nesse cenário, já que é simples simular a interação do JS por meio da <xref:Microsoft.JSInterop.IJSRuntime> interface. |
| Componente que depende de código JS complexo | Testes de unidade e testes de JS separados | Se um componente usa a interoperabilidade JS para chamar uma biblioteca JS grande ou complexa, mas a interação entre o Blazor componente e a biblioteca js é simples, a melhor abordagem provavelmente tratará a biblioteca de componentes e js ou o código como duas partes separadas e testará cada uma individualmente. Teste o Blazor componente com uma biblioteca de testes de unidade e teste o js com uma biblioteca de testes do js. |
| Componente com lógica que depende da manipulação de JS do DOM do navegador | Teste de E2E | Quando a funcionalidade de um componente depende do JS e de sua manipulação do DOM, verifique o JS e o Blazor código juntos em um teste do E2E. Essa é a abordagem que os Blazor desenvolvedores do Framework fizeram com a Blazor lógica de renderização do navegador, que tem um código C# e js rigidamente acoplado. O código C# e JS deve trabalhar juntos para renderizar corretamente os Blazor componentes em um navegador.
| Componente que depende da biblioteca de componentes de terceiros com dependências de difícil simulação | Teste de E2E | Quando a funcionalidade de um componente é dependente de uma biblioteca de componentes de terceiros que tem dependências difíceis de simular, como o JS Interop, o teste E2E pode ser a única opção para testar o componente. |

## <a name="test-components-with-bunit"></a>Componentes de teste com bUnit

Não há uma estrutura de teste da Microsoft oficial para Blazor o, mas o projeto orientado pela Comunidade [bUnit](https://github.com/egil/bUnit) fornece uma maneira conveniente para componentes de teste de unidade Blazor .

> [!NOTE]
> o bUnit é uma biblioteca de testes de terceiros e não tem suporte ou é mantido pela Microsoft.

o bUnit funciona com estruturas de teste de uso geral, como [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/)e [xUnit](https://xunit.github.io/). Essas estruturas de teste fazem com que os testes de bUnit sejam semelhantes aos testes de unidade regulares. os testes de bUnit integrados a uma estrutura de teste de uso geral são normalmente executados com:

* [Gerenciador de testes do Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).
* [`dotnet test`](/dotnet/core/tools/dotnet-test) Comando da CLI em um shell de comando.
* Um pipeline de teste de DevOps automatizado.

> [!NOTE]
> Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos. Consulte a documentação da estrutura de teste para obter diretrizes.

O seguinte demonstra a estrutura de um teste do bUnit no `Counter` componente em um aplicativo baseado em um Blazor modelo de projeto. O `Counter` componente exibe e incrementa um contador com base no usuário que seleciona um botão na página:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

O teste bUnit a seguir verifica se o contador de recorte é incrementado corretamente quando o botão é selecionado:

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

As ações a seguir ocorrem em cada etapa do teste:

* *Organizar*: o `Counter` componente é renderizado usando bUnit `TestContext` . O elemento de parágrafo recortado ( `<p>` ) é encontrado e atribuído a `paraElm` .

* *Act*: o elemento do botão ( `<button>` ) está localizado e, em seguida, selecionado chamando `Click` , que deve incrementar o contador e atualizar o conteúdo da marca de parágrafo ( `<p>` ). O conteúdo de texto do elemento de parágrafo é obtido chamando `TextContent` .

* *Assert*: `MarkupMatches` é chamado no conteúdo de texto para verificar se ele corresponde à cadeia de caracteres esperada, que é `Current count: 1` .

> [!NOTE]
> O `MarkupMatches` método Assert difere de uma asserção de comparação de cadeia de caracteres regular (por exemplo, `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` realiza uma comparação semântica entre a entrada e a marcação HTML esperada. Uma comparação semântica reconhece A semântica HTML, o que significa que coisas como um espaço em branco insignificante são ignoradas. Isso resulta em testes mais estáveis. Para obter mais informações, consulte [Personalizando a comparação de HTML semântico](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução com bUnit](https://bunit.egilhansen.com/docs/getting-started/): as instruções do bUnit incluem orientação sobre como criar um projeto de teste, fazer referência a pacotes de estrutura de teste e compilar e executar testes.
