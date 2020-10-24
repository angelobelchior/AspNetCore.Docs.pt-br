---
title: ASP.NET Core Blazor Associação de dados
author: guardrex
description: Saiba mais sobre os recursos de associação de dados para componentes e elementos DOM em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
ms.openlocfilehash: fd337a6fb54c418ff08af18014073a6b3f07bb8c
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491451"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="2d94b-103">ASP.NET Core Blazor Associação de dados</span><span class="sxs-lookup"><span data-stu-id="2d94b-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="2d94b-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="2d94b-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="2d94b-105">Razor os componentes fornecem recursos de vinculação de dados por meio de um atributo de elemento HTML chamado [`@bind`](xref:mvc/views/razor#bind) com um campo, propriedade ou Razor valor de expressão.</span><span class="sxs-lookup"><span data-stu-id="2d94b-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="2d94b-106">O exemplo a seguir associa um `<input>` elemento ao `currentValue` campo e a um `<input>` elemento à `CurrentValue` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="2d94b-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="2d94b-107">Quando um dos elementos perde o foco, seu campo ou propriedade associada é atualizado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="2d94b-108">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor do campo ou da propriedade.</span><span class="sxs-lookup"><span data-stu-id="2d94b-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="2d94b-109">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de campo e propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="2d94b-110">[`@bind`](xref:mvc/views/razor#bind)O uso da `CurrentValue` Propriedade ( `<input @bind="CurrentValue" />` ) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="2d94b-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2d94b-111">Quando o componente é renderizado, o `value` do elemento de entrada vem da `CurrentValue` propriedade.</span><span class="sxs-lookup"><span data-stu-id="2d94b-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="2d94b-112">Quando o usuário digita na caixa de texto e altera o foco do elemento, o `onchange` evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="2d94b-113">Na realidade, a geração de código é mais complexa do que isso porque o [`@bind`](xref:mvc/views/razor#bind) lida com casos em que as conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="2d94b-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="2d94b-114">Em princípio, [`@bind`](xref:mvc/views/razor#bind) associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="2d94b-115">Associe uma propriedade ou um campo a outros eventos, incluindo também um `@bind:event` atributo com um `event` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="2d94b-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="2d94b-116">O exemplo a seguir associa a `CurrentValue` propriedade no `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="2d94b-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2d94b-117">Ao contrário de `onchange` , que é acionado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="2d94b-118">A associação de atributo diferencia maiúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="2d94b-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="2d94b-119">`@bind` é válido.</span><span class="sxs-lookup"><span data-stu-id="2d94b-119">`@bind` is valid.</span></span>
* <span data-ttu-id="2d94b-120">`@Bind` e `@BIND` são inválidos.</span><span class="sxs-lookup"><span data-stu-id="2d94b-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="2d94b-121">Valores não analisáveis</span><span class="sxs-lookup"><span data-stu-id="2d94b-121">Unparsable values</span></span>

<span data-ttu-id="2d94b-122">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="2d94b-123">Considere o seguinte cenário:</span><span class="sxs-lookup"><span data-stu-id="2d94b-123">Consider the following scenario:</span></span>

* <span data-ttu-id="2d94b-124">Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123` :</span><span class="sxs-lookup"><span data-stu-id="2d94b-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="2d94b-125">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="2d94b-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="2d94b-126">No cenário anterior, o valor do elemento é revertido para `123` .</span><span class="sxs-lookup"><span data-stu-id="2d94b-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="2d94b-127">Quando o valor `123.45` é rejeitado em favor do valor original de `123` , o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="2d94b-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="2d94b-128">Por padrão, a associação aplica-se ao `onchange` evento do elemento ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="2d94b-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="2d94b-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="2d94b-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="2d94b-130">Para o `oninput` evento ( `@bind:event="oninput"` ), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="2d94b-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="2d94b-131">Ao direcionar o `oninput` evento com um `int` tipo associado, um usuário é impedido de digitar um `.` caractere.</span><span class="sxs-lookup"><span data-stu-id="2d94b-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="2d94b-132">Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="2d94b-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="2d94b-133">Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor não analisável `<input>` .</span><span class="sxs-lookup"><span data-stu-id="2d94b-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="2d94b-134">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="2d94b-134">Alternatives include:</span></span>

* <span data-ttu-id="2d94b-135">Não use o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="2d94b-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="2d94b-136">Use o `onchange` evento padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"` ), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="2d94b-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="2d94b-137">Associar a um tipo anulável, como `int?` ou `string` e fornecer lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="2d94b-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="2d94b-138">Use um [componente de validação de formulário](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="2d94b-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="2d94b-139">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="2d94b-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="2d94b-140">Para obter mais informações, consulte <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="2d94b-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="2d94b-141">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="2d94b-141">Form validation components:</span></span>
  * <span data-ttu-id="2d94b-142">Permitir que o usuário forneça erros de entrada e de validação inválidos no associado <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="2d94b-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="2d94b-143">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="2d94b-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="2d94b-144">Formatar cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="2d94b-144">Format strings</span></span>

<span data-ttu-id="2d94b-145">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="2d94b-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="2d94b-146">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="2d94b-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="2d94b-147">No código anterior, o `<input>` tipo de campo do elemento ( `type` ) usa como padrão `text` .</span><span class="sxs-lookup"><span data-stu-id="2d94b-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="2d94b-148">`@bind:format` tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="2d94b-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="2d94b-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="2d94b-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="2d94b-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="2d94b-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="2d94b-151">O `@bind:format` atributo especifica o formato de data a ser aplicado ao `value` do `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="2d94b-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="2d94b-152">O formato também é usado para analisar o valor quando `onchange` ocorre um evento.</span><span class="sxs-lookup"><span data-stu-id="2d94b-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="2d94b-153">Não é recomendável especificar um formato para o `date` tipo de campo porque o Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="2d94b-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="2d94b-154">Apesar da recomendação, use apenas o formato de `yyyy-MM-dd` data para a associação para funcionar corretamente se um formato for fornecido com o `date` tipo de campo:</span><span class="sxs-lookup"><span data-stu-id="2d94b-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="2d94b-155">Associação com parâmetros de componente</span><span class="sxs-lookup"><span data-stu-id="2d94b-155">Binding with component parameters</span></span>

<span data-ttu-id="2d94b-156">Um cenário comum é vincular uma propriedade em um componente filho a uma propriedade em seu pai.</span><span class="sxs-lookup"><span data-stu-id="2d94b-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="2d94b-157">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="2d94b-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="2d94b-158">Os parâmetros de componente permitem a associação de propriedades e campos de um componente pai com `@bind-{PROPERTY OR FIELD}` sintaxe.</span><span class="sxs-lookup"><span data-stu-id="2d94b-158">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="2d94b-159">As associações encadeadas não podem ser implementadas com [`@bind`](xref:mvc/views/razor#bind) a sintaxe no componente filho.</span><span class="sxs-lookup"><span data-stu-id="2d94b-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="2d94b-160">Um manipulador de eventos e um valor devem ser especificados separadamente para dar suporte à atualização da propriedade no pai a partir do componente filho.</span><span class="sxs-lookup"><span data-stu-id="2d94b-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="2d94b-161">O componente pai ainda aproveita a [`@bind`](xref:mvc/views/razor#bind) sintaxe para configurar a vinculação de dados com o componente filho.</span><span class="sxs-lookup"><span data-stu-id="2d94b-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="2d94b-162">O `Child` componente () a seguir `Shared/Child.razor` tem um `Year` parâmetro de componente e um retorno de `YearChanged` chamada:</span><span class="sxs-lookup"><span data-stu-id="2d94b-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="2d94b-163">O retorno de chamada ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) deve ser nomeado como o nome do parâmetro de componente seguido pelo `Changed` sufixo "" ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="2d94b-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="2d94b-164">No exemplo anterior, o retorno de chamada é nomeado `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="2d94b-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="2d94b-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invoca o delegado associado à associação com o argumento fornecido e despacha uma notificação de evento para a propriedade alterada.</span><span class="sxs-lookup"><span data-stu-id="2d94b-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="2d94b-166">No componente a seguir `Parent` ( `Parent.razor` ), o `year` campo está associado ao `Year` parâmetro do componente filho:</span><span class="sxs-lookup"><span data-stu-id="2d94b-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="2d94b-167">O `Year` parâmetro é ligável porque tem um `YearChanged` evento complementar que corresponde ao tipo do `Year` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="2d94b-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="2d94b-168">Por convenção, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um `@bind-{PROPERTY}:event` atributo atribuído ao manipulador.</span><span class="sxs-lookup"><span data-stu-id="2d94b-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="2d94b-169">`<Child @bind-Year="year" />` é equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="2d94b-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="2d94b-170">Em um exemplo mais sofisticado e do mundo real, o seguinte `PasswordField` componente ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="2d94b-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="2d94b-171">Define o `<input>` valor de um elemento para um `password` campo.</span><span class="sxs-lookup"><span data-stu-id="2d94b-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="2d94b-172">Expõe as alterações de uma `Password` propriedade em um componente pai com um [`EventCallback`](xref:blazor/components/event-handling#eventcallback) que passa no valor atual do `password` campo filho como seu argumento.</span><span class="sxs-lookup"><span data-stu-id="2d94b-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="2d94b-173">Usa o `onclick` evento para disparar o `ToggleShowPassword` método.</span><span class="sxs-lookup"><span data-stu-id="2d94b-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="2d94b-174">Para obter mais informações, consulte <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="2d94b-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="2d94b-175">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="2d94b-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="2d94b-176">Executar verificações ou erros de interceptação no método que invoca o delegado da associação.</span><span class="sxs-lookup"><span data-stu-id="2d94b-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="2d94b-177">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="2d94b-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="2d94b-178">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="2d94b-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="2d94b-179">Associar entre mais de dois componentes</span><span class="sxs-lookup"><span data-stu-id="2d94b-179">Bind across more than two components</span></span>

<span data-ttu-id="2d94b-180">Você pode associar qualquer número de componentes aninhados, mas deve respeitar o fluxo de dados unidirecional:</span><span class="sxs-lookup"><span data-stu-id="2d94b-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="2d94b-181">As notificações *de alteração fluem para cima na hierarquia*.</span><span class="sxs-lookup"><span data-stu-id="2d94b-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="2d94b-182">Novos valores *de parâmetro fluem para baixo na hierarquia*.</span><span class="sxs-lookup"><span data-stu-id="2d94b-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="2d94b-183">Uma abordagem comum e recomendada é armazenar apenas os dados subjacentes no componente pai para evitar qualquer confusão sobre qual estado deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="2d94b-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="2d94b-184">Os seguintes componentes demonstram os conceitos anteriores:</span><span class="sxs-lookup"><span data-stu-id="2d94b-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="2d94b-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="2d94b-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="2d94b-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="2d94b-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="2d94b-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="2d94b-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private async Task ChangeValue()
    {
        await PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="2d94b-188">Para obter uma abordagem alternativa adequada ao compartilhamento de dados na memória entre componentes que não estão necessariamente aninhados, consulte <xref:blazor/state-management#in-memory-state-container-service> .</span><span class="sxs-lookup"><span data-stu-id="2d94b-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d94b-189">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2d94b-189">Additional resources</span></span>

* [<span data-ttu-id="2d94b-190">Associação a botões de opção em um formulário</span><span class="sxs-lookup"><span data-stu-id="2d94b-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="2d94b-191">Associando `<select>` Opções de elemento a valores de objeto C# `null` em um formulário</span><span class="sxs-lookup"><span data-stu-id="2d94b-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
