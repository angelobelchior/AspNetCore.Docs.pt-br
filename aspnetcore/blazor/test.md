---
title: Componentes de teste no ASP.NET Core Blazor
author: guardrex
description: Saiba como testar o componments em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 572b9a293e2fd6f51431cd1de6ada737addf5efa
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746527"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a><span data-ttu-id="eb5bf-103">Componentes de teste no ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="eb5bf-103">Test components in ASP.NET Core Blazor</span></span>

[<span data-ttu-id="eb5bf-104">Egil Hansen</span><span class="sxs-lookup"><span data-stu-id="eb5bf-104">Egil Hansen</span></span>](https://egilhansen.com/)

<span data-ttu-id="eb5bf-105">O teste é um aspecto importante da criação de software estável e passível de manutenção.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="eb5bf-106">Para testar um Blazor componente, o *componente em teste* (recortar) é:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="eb5bf-107">Renderizado com a entrada relevante para o teste.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="eb5bf-108">Dependendo do tipo de teste executado, possivelmente sujeito à interação ou modificação.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="eb5bf-109">Por exemplo, os manipuladores de eventos podem ser disparados, como um `onclick` evento para um botão.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="eb5bf-110">Inspecionado os valores esperados.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="eb5bf-111">Abordagens de teste</span><span class="sxs-lookup"><span data-stu-id="eb5bf-111">Test approaches</span></span>

<span data-ttu-id="eb5bf-112">Duas abordagens comuns para testar Blazor componentes são testes de E2E (ponta a ponta) e testes de unidade:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="eb5bf-113">**Teste de unidade**: os [testes de unidade](/dotnet/core/testing/) são gravados com uma biblioteca de testes de unidade que fornece:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="eb5bf-114">Renderização de componente.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-114">Component rendering.</span></span>
  * <span data-ttu-id="eb5bf-115">Inspeção da saída e do estado do componente.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="eb5bf-116">Disparo de manipuladores de eventos e métodos de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="eb5bf-117">Asserções de que o comportamento do componente está correto.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="eb5bf-118">[bUnit](https://github.com/egil/bUnit) é um exemplo de uma biblioteca que habilita o Razor teste de unidade de componente.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="eb5bf-119">**Teste de E2E**: um executor de teste executa um Blazor aplicativo que contém o recorte e automatiza uma instância do navegador.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="eb5bf-120">A ferramenta de teste inspeciona e interage com o corte através do navegador.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="eb5bf-121">[Selenium](https://github.com/SeleniumHQ/selenium) é um exemplo de uma estrutura de teste do e2e que pode ser usada com Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="eb5bf-122">No teste de unidade, apenas o Blazor componente ( Razor /c #) está envolvido.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="eb5bf-123">Dependências externas, como os serviços e a interoperabilidade JS, devem ser simuladas.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="eb5bf-124">No teste do E2E, o Blazor componente e toda a sua infraestrutura auxiliar fazem parte do teste, incluindo CSS, js e APIs de navegador e dom.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="eb5bf-125">*Escopo de teste* descreve a extensão dos testes.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="eb5bf-126">O escopo de teste normalmente tem uma influência sobre a velocidade dos testes.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="eb5bf-127">Os testes de unidade são executados em um subconjunto dos subsistemas do aplicativo e geralmente são executados em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="eb5bf-128">Os testes de E2E, que testam um grupo amplo de subsistemas do aplicativo, podem levar vários segundos para serem concluídos.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="eb5bf-129">O teste de unidade também fornece acesso à instância do recorte, permitindo a inspeção e a verificação do estado interno do componente.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="eb5bf-130">Isso normalmente não é possível no teste de E2E.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="eb5bf-131">Com relação ao ambiente do componente, os testes de E2E devem garantir que o estado ambiental esperado tenha sido atingido antes do início da verificação.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="eb5bf-132">Caso contrário, o resultado será imprevisível.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="eb5bf-133">No teste de unidade, o processamento do recorte e do ciclo de vida do teste são mais integrados, o que melhora a estabilidade do teste.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="eb5bf-134">Os testes de E2E envolvem a inicialização de vários processos, a rede e a e/s de disco e outras atividades de subsistema que geralmente levam a uma confiabilidade de teste ruim.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="eb5bf-135">Os testes de unidade normalmente são isolados desses tipos de problemas.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="eb5bf-136">A tabela a seguir resume a diferença entre as duas abordagens de teste.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="eb5bf-137">Recurso</span><span class="sxs-lookup"><span data-stu-id="eb5bf-137">Capability</span></span>                       | <span data-ttu-id="eb5bf-138">Teste de unidade</span><span class="sxs-lookup"><span data-stu-id="eb5bf-138">Unit testing</span></span>                     | <span data-ttu-id="eb5bf-139">Teste de E2E</span><span class="sxs-lookup"><span data-stu-id="eb5bf-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="eb5bf-140">Escopo do teste</span><span class="sxs-lookup"><span data-stu-id="eb5bf-140">Test scope</span></span>                       | <span data-ttu-id="eb5bf-141">Blazor componente ( Razor apenas/c #)</span><span class="sxs-lookup"><span data-stu-id="eb5bf-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="eb5bf-142">Blazor componente ( Razor /c #) com CSS/JS</span><span class="sxs-lookup"><span data-stu-id="eb5bf-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="eb5bf-143">Tempo de execução do teste</span><span class="sxs-lookup"><span data-stu-id="eb5bf-143">Test execution time</span></span>              | <span data-ttu-id="eb5bf-144">Milissegundos</span><span class="sxs-lookup"><span data-stu-id="eb5bf-144">Milliseconds</span></span>                     | <span data-ttu-id="eb5bf-145">Segundos</span><span class="sxs-lookup"><span data-stu-id="eb5bf-145">Seconds</span></span>                                 |
| <span data-ttu-id="eb5bf-146">Acesso à instância do componente</span><span class="sxs-lookup"><span data-stu-id="eb5bf-146">Access to the component instance</span></span> | <span data-ttu-id="eb5bf-147">Sim</span><span class="sxs-lookup"><span data-stu-id="eb5bf-147">Yes</span></span>                              | <span data-ttu-id="eb5bf-148">Não</span><span class="sxs-lookup"><span data-stu-id="eb5bf-148">No</span></span>                                      |
| <span data-ttu-id="eb5bf-149">Sensível ao ambiente</span><span class="sxs-lookup"><span data-stu-id="eb5bf-149">Sensitive to the environment</span></span>     | <span data-ttu-id="eb5bf-150">Não</span><span class="sxs-lookup"><span data-stu-id="eb5bf-150">No</span></span>                               | <span data-ttu-id="eb5bf-151">Sim</span><span class="sxs-lookup"><span data-stu-id="eb5bf-151">Yes</span></span>                                     |
| <span data-ttu-id="eb5bf-152">Confiabilidade</span><span class="sxs-lookup"><span data-stu-id="eb5bf-152">Reliability</span></span>                      | <span data-ttu-id="eb5bf-153">Mais confiável</span><span class="sxs-lookup"><span data-stu-id="eb5bf-153">More reliable</span></span>                    | <span data-ttu-id="eb5bf-154">Menos confiável</span><span class="sxs-lookup"><span data-stu-id="eb5bf-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="eb5bf-155">Escolha a abordagem de teste mais apropriada</span><span class="sxs-lookup"><span data-stu-id="eb5bf-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="eb5bf-156">Considere o cenário ao escolher o tipo de teste a ser executado.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="eb5bf-157">Algumas considerações são descritas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="eb5bf-158">Cenário</span><span class="sxs-lookup"><span data-stu-id="eb5bf-158">Scenario</span></span> | <span data-ttu-id="eb5bf-159">Abordagem sugerida</span><span class="sxs-lookup"><span data-stu-id="eb5bf-159">Suggested approach</span></span> | <span data-ttu-id="eb5bf-160">Comentários</span><span class="sxs-lookup"><span data-stu-id="eb5bf-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="eb5bf-161">Componente sem lógica de interoperabilidade do JS</span><span class="sxs-lookup"><span data-stu-id="eb5bf-161">Component without JS interop logic</span></span> | <span data-ttu-id="eb5bf-162">Teste de unidade</span><span class="sxs-lookup"><span data-stu-id="eb5bf-162">Unit testing</span></span> | <span data-ttu-id="eb5bf-163">Quando não há nenhuma dependência na interoperabilidade do JS em um Blazor componente, o componente pode ser testado sem acesso ao js ou à API do dom.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="eb5bf-164">Nesse cenário, não há desvantagens em escolher o teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="eb5bf-165">Componente com lógica de interoperabilidade simples do JS</span><span class="sxs-lookup"><span data-stu-id="eb5bf-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="eb5bf-166">Teste de unidade</span><span class="sxs-lookup"><span data-stu-id="eb5bf-166">Unit testing</span></span> | <span data-ttu-id="eb5bf-167">É comum que os componentes consultem o DOM ou disparem animações por meio da interoperabilidade JS.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="eb5bf-168">Em geral, o teste de unidade é preferido nesse cenário, já que é simples simular a interação do JS por meio da <xref:Microsoft.JSInterop.IJSRuntime> interface.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="eb5bf-169">Componente que depende de código JS complexo</span><span class="sxs-lookup"><span data-stu-id="eb5bf-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="eb5bf-170">Testes de unidade e testes de JS separados</span><span class="sxs-lookup"><span data-stu-id="eb5bf-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="eb5bf-171">Se um componente usa a interoperabilidade JS para chamar uma biblioteca JS grande ou complexa, mas a interação entre o Blazor componente e a biblioteca js é simples, a melhor abordagem provavelmente tratará a biblioteca de componentes e js ou o código como duas partes separadas e testará cada uma individualmente.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="eb5bf-172">Teste o Blazor componente com uma biblioteca de testes de unidade e teste o js com uma biblioteca de testes do js.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="eb5bf-173">Componente com lógica que depende da manipulação de JS do DOM do navegador</span><span class="sxs-lookup"><span data-stu-id="eb5bf-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="eb5bf-174">Teste de E2E</span><span class="sxs-lookup"><span data-stu-id="eb5bf-174">E2E testing</span></span> | <span data-ttu-id="eb5bf-175">Quando a funcionalidade de um componente depende do JS e de sua manipulação do DOM, verifique o JS e o Blazor código juntos em um teste do E2E.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="eb5bf-176">Essa é a abordagem que os Blazor desenvolvedores do Framework fizeram com a Blazor lógica de renderização do navegador, que tem um código C# e js rigidamente acoplado.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="eb5bf-177">O código C# e JS deve trabalhar juntos para renderizar corretamente os Blazor componentes em um navegador.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="eb5bf-178">Componente que depende da biblioteca de componentes de terceiros com dependências de difícil simulação</span><span class="sxs-lookup"><span data-stu-id="eb5bf-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="eb5bf-179">Teste de E2E</span><span class="sxs-lookup"><span data-stu-id="eb5bf-179">E2E testing</span></span> | <span data-ttu-id="eb5bf-180">Quando a funcionalidade de um componente é dependente de uma biblioteca de componentes de terceiros que tem dependências difíceis de simular, como o JS Interop, o teste E2E pode ser a única opção para testar o componente.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="eb5bf-181">Componentes de teste com bUnit</span><span class="sxs-lookup"><span data-stu-id="eb5bf-181">Test components with bUnit</span></span>

<span data-ttu-id="eb5bf-182">Não há uma estrutura de teste da Microsoft oficial para Blazor o, mas o projeto orientado pela Comunidade [bUnit](https://github.com/egil/bUnit) fornece uma maneira conveniente para componentes de teste de unidade Blazor .</span><span class="sxs-lookup"><span data-stu-id="eb5bf-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="eb5bf-183">o bUnit é uma biblioteca de testes de terceiros e não tem suporte ou é mantido pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="eb5bf-184">o bUnit funciona com estruturas de teste de uso geral, como [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/)e [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="eb5bf-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="eb5bf-185">Essas estruturas de teste fazem com que os testes de bUnit sejam semelhantes aos testes de unidade regulares.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="eb5bf-186">os testes de bUnit integrados a uma estrutura de teste de uso geral são normalmente executados com:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="eb5bf-187">[Gerenciador de testes do Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="eb5bf-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="eb5bf-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) Comando da CLI em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="eb5bf-189">Um pipeline de teste de DevOps automatizado.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="eb5bf-190">Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="eb5bf-191">Consulte a documentação da estrutura de teste para obter diretrizes.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="eb5bf-192">O seguinte demonstra a estrutura de um teste do bUnit no `Counter` componente em um aplicativo baseado em um Blazor modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="eb5bf-193">O `Counter` componente exibe e incrementa um contador com base no usuário que seleciona um botão na página:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

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

<span data-ttu-id="eb5bf-194">O teste bUnit a seguir verifica se o contador de recorte é incrementado corretamente quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="eb5bf-195">As ações a seguir ocorrem em cada etapa do teste:</span><span class="sxs-lookup"><span data-stu-id="eb5bf-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="eb5bf-196">*Organizar*: o `Counter` componente é renderizado usando bUnit `TestContext` .</span><span class="sxs-lookup"><span data-stu-id="eb5bf-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="eb5bf-197">O elemento de parágrafo recortado ( `<p>` ) é encontrado e atribuído a `paraElm` .</span><span class="sxs-lookup"><span data-stu-id="eb5bf-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="eb5bf-198">*Act*: o elemento do botão ( `<button>` ) está localizado e, em seguida, selecionado chamando `Click` , que deve incrementar o contador e atualizar o conteúdo da marca de parágrafo ( `<p>` ).</span><span class="sxs-lookup"><span data-stu-id="eb5bf-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="eb5bf-199">O conteúdo de texto do elemento de parágrafo é obtido chamando `TextContent` .</span><span class="sxs-lookup"><span data-stu-id="eb5bf-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="eb5bf-200">*Assert*: `MarkupMatches` é chamado no conteúdo de texto para verificar se ele corresponde à cadeia de caracteres esperada, que é `Current count: 1` .</span><span class="sxs-lookup"><span data-stu-id="eb5bf-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="eb5bf-201">O `MarkupMatches` método Assert difere de uma asserção de comparação de cadeia de caracteres regular (por exemplo, `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` realiza uma comparação semântica entre a entrada e a marcação HTML esperada.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="eb5bf-202">Uma comparação semântica reconhece A semântica HTML, o que significa que coisas como um espaço em branco insignificante são ignoradas.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="eb5bf-203">Isso resulta em testes mais estáveis.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-203">This results in more stable tests.</span></span> <span data-ttu-id="eb5bf-204">Para obter mais informações, consulte [Personalizando a comparação de HTML semântico](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="eb5bf-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb5bf-205">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="eb5bf-205">Additional resources</span></span>

* <span data-ttu-id="eb5bf-206">[Introdução com bUnit](https://bunit.egilhansen.com/docs/getting-started/): as instruções do bUnit incluem orientação sobre como criar um projeto de teste, fazer referência a pacotes de estrutura de teste e compilar e executar testes.</span><span class="sxs-lookup"><span data-stu-id="eb5bf-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
