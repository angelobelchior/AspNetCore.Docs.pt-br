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
ms.openlocfilehash: d88cad10314872271250cd43212a64698f485381
ms.sourcegitcommit: 8ed9a413bdc2d665ad11add8828898d726ccb106
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280394"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="14a12-103">ASP.NET Core Blazor Associação de dados</span><span class="sxs-lookup"><span data-stu-id="14a12-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="14a12-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="14a12-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="14a12-105">Razor os componentes fornecem recursos de vinculação de dados por meio de um atributo de elemento HTML chamado [`@bind`](xref:mvc/views/razor#bind) com um campo, propriedade ou Razor valor de expressão.</span><span class="sxs-lookup"><span data-stu-id="14a12-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="14a12-106">O exemplo a seguir associa um `<input>` elemento ao `currentValue` campo e a um `<input>` elemento à `CurrentValue` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="14a12-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="14a12-107">Quando um dos elementos ultrapassa o foco, seu campo ou propriedade associada é atualizado.</span><span class="sxs-lookup"><span data-stu-id="14a12-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="14a12-108">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor do campo ou da propriedade.</span><span class="sxs-lookup"><span data-stu-id="14a12-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="14a12-109">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de campo e propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="14a12-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="14a12-110">[`@bind`](xref:mvc/views/razor#bind)O uso da `CurrentValue` Propriedade ( `<input @bind="CurrentValue" />` ) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="14a12-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="14a12-111">Quando o componente é renderizado, o `value` do elemento de entrada vem da `CurrentValue` propriedade.</span><span class="sxs-lookup"><span data-stu-id="14a12-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="14a12-112">Quando o usuário digita na caixa de texto e altera o foco do elemento, o `onchange` evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="14a12-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="14a12-113">Na realidade, a geração de código é mais complexa do que isso porque o [`@bind`](xref:mvc/views/razor#bind) lida com casos em que as conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="14a12-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="14a12-114">Em princípio, [`@bind`](xref:mvc/views/razor#bind) associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="14a12-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="14a12-115">Associe uma propriedade ou um campo a outros eventos, incluindo também um `@bind:event` atributo com um `event` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="14a12-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="14a12-116">O exemplo a seguir associa a `CurrentValue` propriedade no `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="14a12-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="14a12-117">Ao contrário de `onchange` , que é acionado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="14a12-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="14a12-118">A associação de atributo diferencia maiúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="14a12-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="14a12-119">`@bind` é válido.</span><span class="sxs-lookup"><span data-stu-id="14a12-119">`@bind` is valid.</span></span>
* <span data-ttu-id="14a12-120">`@Bind` e `@BIND` são inválidos.</span><span class="sxs-lookup"><span data-stu-id="14a12-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="14a12-121">Valores não analisáveis</span><span class="sxs-lookup"><span data-stu-id="14a12-121">Unparsable values</span></span>

<span data-ttu-id="14a12-122">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="14a12-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="14a12-123">Considere o cenário a seguir.</span><span class="sxs-lookup"><span data-stu-id="14a12-123">Consider the following scenario:</span></span>

* <span data-ttu-id="14a12-124">Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123` :</span><span class="sxs-lookup"><span data-stu-id="14a12-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="14a12-125">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="14a12-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="14a12-126">No cenário anterior, o valor do elemento é revertido para `123` .</span><span class="sxs-lookup"><span data-stu-id="14a12-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="14a12-127">Quando o valor `123.45` é rejeitado em favor do valor original de `123` , o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="14a12-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="14a12-128">Por padrão, a associação aplica-se ao `onchange` evento do elemento ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="14a12-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="14a12-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="14a12-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="14a12-130">Para o `oninput` evento ( `@bind:event="oninput"` ), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="14a12-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="14a12-131">Ao direcionar o `oninput` evento com um `int` tipo associado, um usuário é impedido de digitar um `.` caractere.</span><span class="sxs-lookup"><span data-stu-id="14a12-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="14a12-132">Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="14a12-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="14a12-133">Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor não analisável `<input>` .</span><span class="sxs-lookup"><span data-stu-id="14a12-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="14a12-134">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="14a12-134">Alternatives include:</span></span>

* <span data-ttu-id="14a12-135">Não use o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="14a12-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="14a12-136">Use o `onchange` evento padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"` ), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="14a12-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="14a12-137">Associar a um tipo anulável, como `int?` ou `string` e fornecer lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="14a12-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="14a12-138">Use um [componente de validação de formulário](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="14a12-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="14a12-139">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="14a12-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="14a12-140">Para obter mais informações, consulte <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="14a12-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="14a12-141">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="14a12-141">Form validation components:</span></span>
  * <span data-ttu-id="14a12-142">Permitir que o usuário forneça erros de entrada e de validação inválidos no associado <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="14a12-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="14a12-143">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="14a12-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="14a12-144">Formatar cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="14a12-144">Format strings</span></span>

<span data-ttu-id="14a12-145">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="14a12-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="14a12-146">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="14a12-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="14a12-147">No código anterior, o `<input>` tipo de campo do elemento ( `type` ) usa como padrão `text` .</span><span class="sxs-lookup"><span data-stu-id="14a12-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="14a12-148">`@bind:format` tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="14a12-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="14a12-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="14a12-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="14a12-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="14a12-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="14a12-151">O `@bind:format` atributo especifica o formato de data a ser aplicado ao `value` do `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="14a12-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="14a12-152">O formato também é usado para analisar o valor quando `onchange` ocorre um evento.</span><span class="sxs-lookup"><span data-stu-id="14a12-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="14a12-153">Não é recomendável especificar um formato para o `date` tipo de campo porque o Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="14a12-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="14a12-154">Apesar da recomendação, use apenas o formato de `yyyy-MM-dd` data para a associação para funcionar corretamente se um formato for fornecido com o `date` tipo de campo:</span><span class="sxs-lookup"><span data-stu-id="14a12-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="14a12-155">Associação de pai para filho com parâmetros de componente</span><span class="sxs-lookup"><span data-stu-id="14a12-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="14a12-156">Os parâmetros de componente permitem a associação de propriedades e campos de um componente pai com `@bind-{PROPERTY OR FIELD}` sintaxe.</span><span class="sxs-lookup"><span data-stu-id="14a12-156">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="14a12-157">O `Child` componente () a seguir `Child.razor` tem um `Year` parâmetro de componente e um retorno de `YearChanged` chamada:</span><span class="sxs-lookup"><span data-stu-id="14a12-157">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="14a12-158">O retorno de chamada ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) deve ser nomeado como o nome do parâmetro de componente seguido pelo `Changed` sufixo "" ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="14a12-158">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="14a12-159">No exemplo anterior, o retorno de chamada é nomeado `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="14a12-159">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="14a12-160">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="14a12-160">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="14a12-161">No componente a seguir `Parent` ( `Parent.razor` ), o `year` campo está associado ao `Year` parâmetro do componente filho:</span><span class="sxs-lookup"><span data-stu-id="14a12-161">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

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

<span data-ttu-id="14a12-162">O `Year` parâmetro é ligável porque tem um `YearChanged` evento complementar que corresponde ao tipo do `Year` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="14a12-162">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="14a12-163">Por convenção, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um `@bind-{PROPERTY}:event` atributo atribuído ao manipulador.</span><span class="sxs-lookup"><span data-stu-id="14a12-163">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="14a12-164">`<Child @bind-Year="year" />` é equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="14a12-164">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="14a12-165">Associação de filho para pai com associação encadeada</span><span class="sxs-lookup"><span data-stu-id="14a12-165">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="14a12-166">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="14a12-166">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="14a12-167">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="14a12-167">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="14a12-168">Uma associação encadeada não pode ser implementada com [`@bind`](xref:mvc/views/razor#bind) a sintaxe no componente filho.</span><span class="sxs-lookup"><span data-stu-id="14a12-168">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="14a12-169">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="14a12-169">The event handler and value must be specified separately.</span></span> <span data-ttu-id="14a12-170">Um componente pai, no entanto, pode usar [`@bind`](xref:mvc/views/razor#bind) a sintaxe com o parâmetro do componente filho.</span><span class="sxs-lookup"><span data-stu-id="14a12-170">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="14a12-171">O seguinte `PasswordField` componente ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="14a12-171">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="14a12-172">Define o `<input>` valor de um elemento para uma `Password` propriedade.</span><span class="sxs-lookup"><span data-stu-id="14a12-172">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="14a12-173">Expõe as alterações da `Password` propriedade para um componente pai com um [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="14a12-173">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="14a12-174">Usa o `onclick` evento para disparar o `ToggleShowPassword` método.</span><span class="sxs-lookup"><span data-stu-id="14a12-174">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="14a12-175">Para obter mais informações, consulte <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="14a12-175">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="14a12-176">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="14a12-176">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="14a12-177">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="14a12-177">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="14a12-178">Crie um campo de backup para `Password` ( `password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="14a12-178">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="14a12-179">Execute os erros de verificação ou interceptação no `Password` setter.</span><span class="sxs-lookup"><span data-stu-id="14a12-179">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="14a12-180">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="14a12-180">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="14a12-181">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="14a12-181">Additional resources</span></span>

* [<span data-ttu-id="14a12-182">Associação a botões de opção em um formulário</span><span class="sxs-lookup"><span data-stu-id="14a12-182">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="14a12-183">Associando `<select>` Opções de elemento a valores de objeto C# `null` em um formulário</span><span class="sxs-lookup"><span data-stu-id="14a12-183">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
