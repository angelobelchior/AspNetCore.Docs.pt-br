---
title: Auxiliar de marca de componente no ASP.NET Core
author: guardrex
ms.author: riande
description: Saiba como usar o auxiliar de marca de componente ASP.NET Core para renderizar Razor componentes em páginas e exibições.
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595448"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Auxiliar de marca de componente no ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-5.0"

Siga as orientações na seção de *configuração* para:

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Siga as orientações na seção *configuração* do <xref:blazor/components/prerendering-and-integration?pivots=server> artigo.

::: moniker-end

## <a name="component-tag-helper"></a>Auxiliar de marca de componente

Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` marca).

> [!NOTE]
> A integração de Razor componentes em Razor páginas e aplicativos MVC em um *Blazor WebAssembly aplicativo hospedado* tem suporte no ASP.NET Core no .NET 5,0 ou posterior.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly os modos de renderização do aplicativo são mostrados na tabela a seguir.

| Modo de renderização | Description |
| ----------- | ----------- |
| `WebAssembly` | Renderiza um marcador para um Blazor WebAssembly aplicativo para ser usado para incluir um componente interativo quando carregado no navegador. O componente não é renderizado. Essa opção torna mais fácil renderizar diferentes Blazor WebAssembly componentes em páginas diferentes. |
| `WebAssemblyPrerendered` | Coloca o componente em HTML estático e inclui um marcador para um Blazor WebAssembly aplicativo para uso posterior para tornar o componente interativo quando carregado no navegador. |

Blazor Server os modos de renderização do aplicativo são mostrados na tabela a seguir.

| Modo de renderização | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e inclui um marcador para um Blazor Server aplicativo. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor Server aplicativo. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o componente em HTML estático. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server os modos de renderização do aplicativo são mostrados na tabela a seguir.

| Modo de renderização | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e inclui um marcador para um Blazor Server aplicativo. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor Server aplicativo. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o componente em HTML estático. |

::: moniker-end

Características adicionais incluem:

* São permitidos vários auxiliares de marca de componente que processam vários Razor componentes.
* Os componentes não podem ser processados dinamicamente depois que o aplicativo é iniciado.
* Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro. Os componentes não podem usar recursos de exibição e específicos de página, como exibições parciais e seções. Para usar a lógica de uma exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.
* Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

O auxiliar de marca de componente a seguir renderiza o `Counter` componente em uma página ou exibição em um Blazor Server aplicativo com `ServerPrerendered` :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O exemplo anterior pressupõe que o `Counter` componente está na pasta *páginas* do aplicativo. O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample.Pages` ou `@using BlazorSample.Client.Pages` em uma solução hospedada Blazor ).

O auxiliar de marca de componente também pode passar parâmetros para componentes. Considere o seguinte `ColorfulCheckbox` componente que define a cor e o tamanho do rótulo da caixa de seleção:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

Os `Size` `int` parâmetros de componente () e `Color` ( `string` ) podem ser definidos pelo auxiliar de marca do componente: [component parameters](xref:blazor/components/index#component-parameters)

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

O exemplo anterior pressupõe que o `ColorfulCheckbox` componente está na pasta *compartilhada* do aplicativo. O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample.Shared` ).

O HTML a seguir é renderizado na página ou exibição:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

A passagem de uma cadeia de caracteres entre aspas requer uma [ Razor expressão explícita](xref:mvc/views/razor#explicit-razor-expressions), conforme mostrado `param-Color` no exemplo anterior. O Razor comportamento de análise para um `string` valor de tipo não se aplica a um `param-*` atributo porque o atributo é um `object` tipo.

Todos os tipos de parâmetros têm suporte, exceto:

* Parâmetros genéricos.
* Parâmetros não serializáveis.
* Herança em parâmetros de coleção.
* Parâmetros cujo tipo é definido fora do Blazor WebAssembly aplicativo ou dentro de um assembly carregado lentamente.

O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão. Por exemplo, você pode especificar um valor para `Size` e `Color` no exemplo anterior, porque os tipos de `Size` e `Color` são tipos primitivos ( `int` e `string` ), que são suportados pelo serializador JSON.

No exemplo a seguir, um objeto de classe é passado para o componente:

*MyClass.cs* :

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**A classe deve ter um construtor público sem parâmetros.**

*Compartilhado/myComponent. Razor* :

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Páginas/mypage. cshtml* :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

O exemplo anterior pressupõe que o `MyComponent` componente está na pasta *compartilhada* do aplicativo. O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample` e `@using BlazorSample.Shared` ). `MyClass` está no namespace do aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
