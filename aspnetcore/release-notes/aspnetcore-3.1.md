---
title: O que há de novo no ASP.NET Core 3,1
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: 68373c39461be896a52627e21577fdda89cbb661
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019581"
---
# <a name="whats-new-in-aspnet-core-31"></a>O que há de novo no ASP.NET Core 3,1

Este artigo destaca as alterações mais significativas no ASP.NET Core 3,1 com links para a documentação relevante.

## <a name="partial-class-support-for-no-locrazor-components"></a>Suporte de classe parcial para Razor componentes

RazorAgora, os componentes são gerados como classes parciais. O código para um Razor componente pode ser escrito usando um arquivo code-behind definido como uma classe parcial, em vez de definir todo o código para o componente em um único arquivo. Para obter mais informações, consulte [suporte de classe parcial](xref:blazor/components/index#partial-class-support).

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorAuxiliar de marca de componente e passar parâmetros para componentes de nível superior

No Blazor com ASP.NET Core 3,0, os componentes eram renderizados em páginas e exibições usando um auxiliar HTML ( `Html.RenderComponentAsync` ). No ASP.NET Core 3,1, processe um componente de uma página ou exibição com o novo auxiliar de marca de componente:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O auxiliar HTML permanece com suporte no ASP.NET Core 3,1, mas o auxiliar de marca de componente é recomendado.

Blazor Serveros aplicativos agora podem passar parâmetros para componentes de nível superior durante a renderização inicial. Anteriormente, era possível apenas passar parâmetros para um componente de nível superior com [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Com essa versão, os [processmode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) e [RenderingMode. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) têm suporte. Todos os valores de parâmetro especificados são serializados como JSON e incluídos na resposta inicial.

Por exemplo, PreRender um `Counter` componente com um valor de incremento ( `IncrementAmount` ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Para obter mais informações, consulte [integrar componentes em Razor páginas e aplicativos MVC](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>Suporte para filas compartilhadas no HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) dá suporte à criação de filas de solicitação anônimas. No ASP.NET Core 3,1, adicionamos a capacidade de criar ou anexar a uma fila de solicitações HTTP.sys nomeada existente. Criar ou anexar a uma fila de solicitação chamada HTTP.sys existente habilita cenários em que o processo do controlador de HTTP.sys que possui a fila é independente do processo do ouvinte. Essa independência possibilita preservar as conexões existentes e as solicitações enfileiradas entre as reinicializações do processo de ouvinte:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>Alterações recentes para SameSite cookie s

O comportamento de SameSite cookie s foi alterado para refletir as próximas alterações do navegador. Isso pode afetar os cenários de autenticação como AzureAd, OpenIdConnect ou WsFederation. Para obter mais informações, consulte <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Impedir ações padrão para eventos em Blazor aplicativos

Use o `@on{EVENT}:preventDefault` atributo diretiva para impedir a ação padrão para um evento. No exemplo a seguir, a ação padrão de exibir o caractere da chave na caixa de texto é impedida:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Para obter mais informações, consulte [impedir ações padrão](xref:blazor/components/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Parar a propagação de eventos em Blazor aplicativos

Use o `@on{EVENT}:stopPropagation` atributo diretiva para parar a propagação do evento. No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do filho `<div>` se propaguem para o pai `<div>` :

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Para obter mais informações, consulte [parar a propagação do evento](xref:blazor/components/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Erros detalhados durante o Blazor desenvolvimento de aplicativos

Quando um Blazor aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema. Quando ocorre um erro, os Blazor aplicativos exibem uma barra de ouro na parte inferior da tela:

* Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.
* Em produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.

Para obter mais informações, consulte [erros detalhados durante o desenvolvimento](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).
