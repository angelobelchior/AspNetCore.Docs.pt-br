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
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor Associação de dados

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razor os componentes fornecem recursos de vinculação de dados por meio de um atributo de elemento HTML chamado [`@bind`](xref:mvc/views/razor#bind) com um campo, propriedade ou Razor valor de expressão.

O exemplo a seguir associa um `<input>` elemento ao `currentValue` campo e a um `<input>` elemento à `CurrentValue` Propriedade:

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

Quando um dos elementos ultrapassa o foco, seu campo ou propriedade associada é atualizado.

A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor do campo ou da propriedade. Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de campo e propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.

[`@bind`](xref:mvc/views/razor#bind)O uso da `CurrentValue` Propriedade ( `<input @bind="CurrentValue" />` ) é essencialmente equivalente ao seguinte:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando o componente é renderizado, o `value` do elemento de entrada vem da `CurrentValue` propriedade. Quando o usuário digita na caixa de texto e altera o foco do elemento, o `onchange` evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado. Na realidade, a geração de código é mais complexa do que isso porque o [`@bind`](xref:mvc/views/razor#bind) lida com casos em que as conversões de tipo são executadas. Em princípio, [`@bind`](xref:mvc/views/razor#bind) associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.

Associe uma propriedade ou um campo a outros eventos, incluindo também um `@bind:event` atributo com um `event` parâmetro. O exemplo a seguir associa a `CurrentValue` propriedade no `oninput` evento:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Ao contrário de `onchange` , que é acionado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

A associação de atributo diferencia maiúsculas de minúsculas:

* `@bind` é válido.
* `@Bind` e `@BIND` são inválidos.

## <a name="unparsable-values"></a>Valores não analisáveis

Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.

Considere o cenário a seguir.

* Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123` :

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.

No cenário anterior, o valor do elemento é revertido para `123` . Quando o valor `123.45` é rejeitado em favor do valor original de `123` , o usuário entende que seu valor não foi aceito.

Por padrão, a associação aplica-se ao `onchange` evento do elemento ( `@bind="{PROPERTY OR FIELD}"` ). Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente. Para o `oninput` evento ( `@bind:event="oninput"` ), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável. Ao direcionar o `oninput` evento com um `int` tipo associado, um usuário é impedido de digitar um `.` caractere. Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos. Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor não analisável `<input>` . As alternativas incluem:

* Não use o `oninput` evento. Use o `onchange` evento padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"` ), em que um valor inválido não é revertido até que o elemento perca o foco.
* Associar a um tipo anulável, como `int?` ou `string` e fornecer lógica personalizada para manipular entradas inválidas.
* Use um [componente de validação de formulário](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas. Para obter mais informações, consulte <xref:blazor/forms-validation>. Componentes de validação de formulário:
  * Permitir que o usuário forneça erros de entrada e de validação inválidos no associado <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.

## <a name="format-strings"></a>Formatar cadeias de caracteres

A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando `@bind:format` . Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

No código anterior, o `<input>` tipo de campo do elemento ( `type` ) usa como padrão `text` . `@bind:format` tem suporte para ligar os seguintes tipos .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

O `@bind:format` atributo especifica o formato de data a ser aplicado ao `value` do `<input>` elemento. O formato também é usado para analisar o valor quando `onchange` ocorre um evento.

Não é recomendável especificar um formato para o `date` tipo de campo porque o Blazor tem suporte interno para formatar datas. Apesar da recomendação, use apenas o formato de `yyyy-MM-dd` data para a associação para funcionar corretamente se um formato for fornecido com o `date` tipo de campo:

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Associação de pai para filho com parâmetros de componente

Os parâmetros de componente permitem a associação de propriedades e campos de um componente pai com `@bind-{PROPERTY OR FIELD}` sintaxe.

O `Child` componente () a seguir `Child.razor` tem um `Year` parâmetro de componente e um retorno de `YearChanged` chamada:

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

O retorno de chamada ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) deve ser nomeado como o nome do parâmetro de componente seguido pelo `Changed` sufixo "" ( `{PARAMETER NAME}Changed` ). No exemplo anterior, o retorno de chamada é nomeado `YearChanged` . Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.

No componente a seguir `Parent` ( `Parent.razor` ), o `year` campo está associado ao `Year` parâmetro do componente filho:

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

O `Year` parâmetro é ligável porque tem um `YearChanged` evento complementar que corresponde ao tipo do `Year` parâmetro.

Por convenção, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um `@bind-{PROPERTY}:event` atributo atribuído ao manipulador. `<Child @bind-Year="year" />` é equivalente a escrever:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Associação de filho para pai com associação encadeada

Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente. Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.

Uma associação encadeada não pode ser implementada com [`@bind`](xref:mvc/views/razor#bind) a sintaxe no componente filho. O manipulador de eventos e o valor devem ser especificados separadamente. Um componente pai, no entanto, pode usar [`@bind`](xref:mvc/views/razor#bind) a sintaxe com o parâmetro do componente filho.

O seguinte `PasswordField` componente ( `PasswordField.razor` ):

* Define o `<input>` valor de um elemento para uma `Password` propriedade.
* Expõe as alterações da `Password` propriedade para um componente pai com um [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .
* Usa o `onclick` evento para disparar o `ToggleShowPassword` método. Para obter mais informações, consulte <xref:blazor/components/event-handling>.

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

O `PasswordField` componente é usado em outro componente:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Para executar verificações ou interceptar erros na senha no exemplo anterior:

* Crie um campo de backup para `Password` ( `password` no código de exemplo a seguir).
* Execute os erros de verificação ou interceptação no `Password` setter.

O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:

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

## <a name="additional-resources"></a>Recursos adicionais

* [Associação a botões de opção em um formulário](xref:blazor/forms-validation#radio-buttons)
* [Associando `<select>` Opções de elemento a valores de objeto C# `null` em um formulário](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
