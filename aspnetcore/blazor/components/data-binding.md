---
title: ASP.NET Core Blazor Associação de dados
author: guardrex
description: Saiba mais sobre os recursos de associação de dados para componentes e elementos DOM em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628554"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="f6d8b-103">ASP.NET Core Blazor Associação de dados</span><span class="sxs-lookup"><span data-stu-id="f6d8b-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="f6d8b-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f6d8b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f6d8b-105">Razor os componentes fornecem recursos de vinculação de dados por meio de um atributo de elemento HTML chamado [`@bind`](xref:mvc/views/razor#bind) com um campo, propriedade ou Razor valor de expressão.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="f6d8b-106">O exemplo a seguir associa um `<input>` elemento ao `currentValue` campo e a um `<input>` elemento à `CurrentValue` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f6d8b-107">Quando um dos elementos ultrapassa o foco, seu campo ou propriedade associada é atualizado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="f6d8b-108">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor do campo ou da propriedade.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="f6d8b-109">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de campo e propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="f6d8b-110">[`@bind`](xref:mvc/views/razor#bind)O uso da `CurrentValue` Propriedade ( `<input @bind="CurrentValue" />` ) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f6d8b-111">Quando o componente é renderizado, o `value` do elemento de entrada vem da `CurrentValue` propriedade.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="f6d8b-112">Quando o usuário digita na caixa de texto e altera o foco do elemento, o `onchange` evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="f6d8b-113">Na realidade, a geração de código é mais complexa do que isso porque o [`@bind`](xref:mvc/views/razor#bind) lida com casos em que as conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="f6d8b-114">Em princípio, [`@bind`](xref:mvc/views/razor#bind) associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="f6d8b-115">Associe uma propriedade ou um campo a outros eventos, incluindo também um `@bind:event` atributo com um `event` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="f6d8b-116">O exemplo a seguir associa a `CurrentValue` propriedade no `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f6d8b-117">Ao contrário de `onchange` , que é acionado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="f6d8b-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` sintaxe para associar atributos de elemento diferentes de `value` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="f6d8b-119">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-119">In the following example:</span></span>

* <span data-ttu-id="f6d8b-120">O estilo do parágrafo é **vermelho** quando o componente carrega ( `style="color:red"` ).</span><span class="sxs-lookup"><span data-stu-id="f6d8b-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="f6d8b-121">O usuário altera o valor da caixa de texto para refletir um estilo de cor CSS diferente e altera o foco do elemento da página.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="f6d8b-122">Por exemplo, o usuário altera o valor da caixa de texto para `color:blue` e pressiona a tecla <kbd>Tab</kbd> no teclado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="f6d8b-123">Quando o foco do elemento é alterado:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-123">When the element focus changes:</span></span>
  * <span data-ttu-id="f6d8b-124">O valor de `paragraphStyle` é atribuído a partir do `<input>` valor do elemento.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="f6d8b-125">O estilo de parágrafo é atualizado para refletir o novo estilo no `paragraphStyle` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="f6d8b-126">Se o estilo for atualizado para `color:blue` , a cor do texto será alterada para **azul**.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="f6d8b-127">A associação de atributo diferencia maiúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="f6d8b-128">`@bind` é válido.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-128">`@bind` is valid.</span></span>
* <span data-ttu-id="f6d8b-129">`@Bind` e `@BIND` são inválidos.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="f6d8b-130">Valores não analisáveis</span><span class="sxs-lookup"><span data-stu-id="f6d8b-130">Unparsable values</span></span>

<span data-ttu-id="f6d8b-131">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="f6d8b-132">Considere o cenário a seguir.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-132">Consider the following scenario:</span></span>

* <span data-ttu-id="f6d8b-133">Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123` :</span><span class="sxs-lookup"><span data-stu-id="f6d8b-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="f6d8b-134">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="f6d8b-135">No cenário anterior, o valor do elemento é revertido para `123` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="f6d8b-136">Quando o valor `123.45` é rejeitado em favor do valor original de `123` , o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="f6d8b-137">Por padrão, a associação aplica-se ao `onchange` evento do elemento ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="f6d8b-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="f6d8b-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="f6d8b-139">Para o `oninput` evento ( `@bind:event="oninput"` ), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="f6d8b-140">Ao direcionar o `oninput` evento com um `int` tipo associado, um usuário é impedido de digitar um `.` caractere.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="f6d8b-141">Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="f6d8b-142">Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor não analisável `<input>` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="f6d8b-143">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-143">Alternatives include:</span></span>

* <span data-ttu-id="f6d8b-144">Não use o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="f6d8b-145">Use o `onchange` evento padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"` ), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="f6d8b-146">Associar a um tipo anulável, como `int?` ou `string` e fornecer lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="f6d8b-147">Use um [componente de validação de formulário](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="f6d8b-148">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="f6d8b-149">Para obter mais informações, consulte <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="f6d8b-150">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-150">Form validation components:</span></span>
  * <span data-ttu-id="f6d8b-151">Permitir que o usuário forneça erros de entrada e de validação inválidos no associado <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="f6d8b-152">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="f6d8b-153">Formatar cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="f6d8b-153">Format strings</span></span>

<span data-ttu-id="f6d8b-154">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="f6d8b-155">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="f6d8b-156">No código anterior, o `<input>` tipo de campo do elemento ( `type` ) usa como padrão `text` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="f6d8b-157">`@bind:format` tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="f6d8b-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="f6d8b-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="f6d8b-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="f6d8b-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="f6d8b-160">O `@bind:format` atributo especifica o formato de data a ser aplicado ao `value` do `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="f6d8b-161">O formato também é usado para analisar o valor quando `onchange` ocorre um evento.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="f6d8b-162">Não é recomendável especificar um formato para o `date` tipo de campo porque o Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="f6d8b-163">Apesar da recomendação, use apenas o formato de `yyyy-MM-dd` data para a associação para funcionar corretamente se um formato for fornecido com o `date` tipo de campo:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="f6d8b-164">Associação de pai para filho com parâmetros de componente</span><span class="sxs-lookup"><span data-stu-id="f6d8b-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="f6d8b-165">Os parâmetros de componente permitem a associação de propriedades e campos de um componente pai com `@bind-{PROPERTY OR FIELD}` sintaxe.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="f6d8b-166">O `Child` componente () a seguir `Child.razor` tem um `Year` parâmetro de componente e um retorno de `YearChanged` chamada:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="f6d8b-167">O retorno de chamada ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) deve ser nomeado como o nome do parâmetro de componente seguido pelo `Changed` sufixo "" ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="f6d8b-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="f6d8b-168">No exemplo anterior, o retorno de chamada é nomeado `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="f6d8b-169">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="f6d8b-170">No componente a seguir `Parent` ( `Parent.razor` ), o `year` campo está associado ao `Year` parâmetro do componente filho:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="f6d8b-171">O `Year` parâmetro é ligável porque tem um `YearChanged` evento complementar que corresponde ao tipo do `Year` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="f6d8b-172">Por convenção, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um `@bind-{PROPERTY}:event` atributo atribuído ao manipulador.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="f6d8b-173">`<Child @bind-Year="year" />` é equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="f6d8b-174">Associação de filho para pai com associação encadeada</span><span class="sxs-lookup"><span data-stu-id="f6d8b-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="f6d8b-175">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="f6d8b-176">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="f6d8b-177">Uma associação encadeada não pode ser implementada com [`@bind`](xref:mvc/views/razor#bind) a sintaxe no componente filho.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="f6d8b-178">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="f6d8b-179">Um componente pai, no entanto, pode usar [`@bind`](xref:mvc/views/razor#bind) a sintaxe com o parâmetro do componente filho.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="f6d8b-180">O seguinte `PasswordField` componente ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f6d8b-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="f6d8b-181">Define o `<input>` valor de um elemento para uma `Password` propriedade.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="f6d8b-182">Expõe as alterações da `Password` propriedade para um componente pai com um [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="f6d8b-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="f6d8b-183">Usa o `onclick` evento para disparar o `ToggleShowPassword` método.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="f6d8b-184">Para obter mais informações, consulte <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="f6d8b-185">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="f6d8b-186">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="f6d8b-187">Crie um campo de backup para `Password` ( `password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="f6d8b-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="f6d8b-188">Execute os erros de verificação ou interceptação no `Password` setter.</span><span class="sxs-lookup"><span data-stu-id="f6d8b-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="f6d8b-189">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="f6d8b-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="f6d8b-190">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f6d8b-190">Additional resources</span></span>

* [<span data-ttu-id="f6d8b-191">Associação a botões de opção em um formulário</span><span class="sxs-lookup"><span data-stu-id="f6d8b-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="f6d8b-192">Associando `<select>` Opções de elemento a valores de objeto C# `null` em um formulário</span><span class="sxs-lookup"><span data-stu-id="f6d8b-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
