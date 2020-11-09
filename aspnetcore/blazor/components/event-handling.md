---
title: 'Manipulação de eventos de ASP.NET Core Blazor'
author: guardrex
description: 'Saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/event-handling
ms.openlocfilehash: d17547e7fb8a628a4864209d9857ac828f77c701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056277"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="373e0-103">Manipulação de eventos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="373e0-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="373e0-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="373e0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="373e0-105">Razor os componentes fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="373e0-105">Razor components provide event handling features.</span></span> <span data-ttu-id="373e0-106">Para um atributo de elemento HTML chamado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por exemplo, `@onclick` ) com um valor de tipo delegado, um Razor componente trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="373e0-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="373e0-107">O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="373e0-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="373e0-108">O código a seguir chama o `CheckChanged` método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="373e0-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="373e0-109">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="373e0-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="373e0-110">Não é necessário chamar [StateHasChanged](xref:blazor/components/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="373e0-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="373e0-111">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="373e0-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="373e0-112">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="373e0-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="373e0-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="373e0-113">Event argument types</span></span>

<span data-ttu-id="373e0-114">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="373e0-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="373e0-115">A especificação de um parâmetro de evento em uma definição de método de evento é opcional e será necessária somente se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="373e0-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="373e0-116">No exemplo a seguir, o `MouseEventArgs` argumento de evento é usado no `ShowMessage` método para definir o texto da mensagem:</span><span class="sxs-lookup"><span data-stu-id="373e0-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="373e0-117">Com suporte, <xref:System.EventArgs> são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="373e0-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="373e0-118">Evento</span><span class="sxs-lookup"><span data-stu-id="373e0-118">Event</span></span>            | <span data-ttu-id="373e0-119">Classe</span><span class="sxs-lookup"><span data-stu-id="373e0-119">Class</span></span>  | <span data-ttu-id="373e0-120">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="373e0-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="373e0-121">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="373e0-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="373e0-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="373e0-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="373e0-123">Arrastar</span><span class="sxs-lookup"><span data-stu-id="373e0-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="373e0-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="373e0-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="373e0-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> mantenha arrastado os dados do item.</span><span class="sxs-lookup"><span data-stu-id="373e0-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="373e0-126">Implemente o recurso arrastar e soltar em Blazor aplicativos usando a [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) com a [API de arrastar e soltar HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="373e0-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="373e0-127">Erro</span><span class="sxs-lookup"><span data-stu-id="373e0-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="373e0-128">Evento</span><span class="sxs-lookup"><span data-stu-id="373e0-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="373e0-129">*Geral*</span><span class="sxs-lookup"><span data-stu-id="373e0-129">*General*</span></span><br><span data-ttu-id="373e0-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="373e0-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="373e0-131">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="373e0-131">*Clipboard*</span></span><br><span data-ttu-id="373e0-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="373e0-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="373e0-133">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="373e0-133">*Input*</span></span><br><span data-ttu-id="373e0-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="373e0-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="373e0-135">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="373e0-135">*Media*</span></span><br><span data-ttu-id="373e0-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="373e0-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="373e0-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> mantém atributos para configurar os mapeamentos entre os nomes de evento e os tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="373e0-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="373e0-138">Foco</span><span class="sxs-lookup"><span data-stu-id="373e0-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="373e0-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="373e0-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="373e0-140">Não inclui suporte para `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="373e0-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="373e0-141">Entrada</span><span class="sxs-lookup"><span data-stu-id="373e0-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="373e0-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="373e0-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="373e0-143">Teclado</span><span class="sxs-lookup"><span data-stu-id="373e0-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="373e0-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="373e0-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="373e0-145">Mouse</span><span class="sxs-lookup"><span data-stu-id="373e0-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="373e0-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="373e0-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="373e0-147">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="373e0-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="373e0-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="373e0-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="373e0-149">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="373e0-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="373e0-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="373e0-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="373e0-151">Progresso</span><span class="sxs-lookup"><span data-stu-id="373e0-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="373e0-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="373e0-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="373e0-153">Touch</span><span class="sxs-lookup"><span data-stu-id="373e0-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="373e0-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="373e0-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="373e0-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="373e0-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="373e0-156">Evento</span><span class="sxs-lookup"><span data-stu-id="373e0-156">Event</span></span>            | <span data-ttu-id="373e0-157">Classe</span><span class="sxs-lookup"><span data-stu-id="373e0-157">Class</span></span> | <span data-ttu-id="373e0-158">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="373e0-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="373e0-159">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="373e0-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="373e0-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="373e0-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="373e0-161">Arrastar</span><span class="sxs-lookup"><span data-stu-id="373e0-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="373e0-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="373e0-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="373e0-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> mantenha arrastado os dados do item.</span><span class="sxs-lookup"><span data-stu-id="373e0-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="373e0-164">Implemente o recurso arrastar e soltar em Blazor aplicativos usando a [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) com a [API de arrastar e soltar HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="373e0-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="373e0-165">Erro</span><span class="sxs-lookup"><span data-stu-id="373e0-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="373e0-166">Evento</span><span class="sxs-lookup"><span data-stu-id="373e0-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="373e0-167">*Geral*</span><span class="sxs-lookup"><span data-stu-id="373e0-167">*General*</span></span><br><span data-ttu-id="373e0-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="373e0-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="373e0-169">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="373e0-169">*Clipboard*</span></span><br><span data-ttu-id="373e0-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="373e0-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="373e0-171">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="373e0-171">*Input*</span></span><br><span data-ttu-id="373e0-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="373e0-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="373e0-173">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="373e0-173">*Media*</span></span><br><span data-ttu-id="373e0-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="373e0-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="373e0-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> mantém atributos para configurar os mapeamentos entre os nomes de evento e os tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="373e0-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="373e0-176">Foco</span><span class="sxs-lookup"><span data-stu-id="373e0-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="373e0-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="373e0-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="373e0-178">Não inclui suporte para `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="373e0-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="373e0-179">Entrada</span><span class="sxs-lookup"><span data-stu-id="373e0-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="373e0-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="373e0-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="373e0-181">Teclado</span><span class="sxs-lookup"><span data-stu-id="373e0-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="373e0-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="373e0-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="373e0-183">Mouse</span><span class="sxs-lookup"><span data-stu-id="373e0-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="373e0-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="373e0-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="373e0-185">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="373e0-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="373e0-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="373e0-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="373e0-187">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="373e0-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="373e0-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="373e0-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="373e0-189">Progresso</span><span class="sxs-lookup"><span data-stu-id="373e0-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="373e0-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="373e0-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="373e0-191">Touch</span><span class="sxs-lookup"><span data-stu-id="373e0-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="373e0-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="373e0-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="373e0-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="373e0-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="373e0-194">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="373e0-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="373e0-195">[ `EventArgs` classes na fonte de referência de ASP.NET Core ( `master` ramificação dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="373e0-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="373e0-196">A `master` ramificação representa a API em desenvolvimento para a *próxima* versão de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="373e0-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="373e0-197">Para a versão atual, selecione o Branch do repositório GitHub apropriado (por exemplo, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="373e0-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="373e0-198">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.</span><span class="sxs-lookup"><span data-stu-id="373e0-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="373e0-199">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="373e0-199">Lambda expressions</span></span>

<span data-ttu-id="373e0-200">As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="373e0-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="373e0-201">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="373e0-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="373e0-202">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e seu número de botão ( `buttonNumber` ) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="373e0-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="373e0-203">**Não** use uma variável de loop diretamente em uma expressão lambda, como `i` no exemplo de `for` loop anterior.</span><span class="sxs-lookup"><span data-stu-id="373e0-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="373e0-204">Caso contrário, a mesma variável é usada por todas as expressões lambda, o que resulta em uso do mesmo valor em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="373e0-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="373e0-205">Sempre Capture o valor da variável em uma variável local e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="373e0-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="373e0-206">No exemplo anterior, a variável de loop `i` é atribuída a `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="373e0-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="373e0-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="373e0-207">EventCallback</span></span>

<span data-ttu-id="373e0-208">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho.</span><span class="sxs-lookup"><span data-stu-id="373e0-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="373e0-209">Um `onclick` evento que ocorre no componente filho é um caso de uso comum.</span><span class="sxs-lookup"><span data-stu-id="373e0-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="373e0-210">Para expor eventos entre componentes, use um <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="373e0-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="373e0-211">Um componente pai pode atribuir um método de retorno de chamada a um componente filho <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="373e0-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="373e0-212">O `ChildComponent` no aplicativo de exemplo ( `Components/ChildComponent.razor` ) demonstra como o manipulador de um botão `onclick` é configurado para receber um <xref:Microsoft.AspNetCore.Components.EventCallback> delegado de exemplo `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="373e0-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="373e0-213">O <xref:Microsoft.AspNetCore.Components.EventCallback> é digitado com `MouseEventArgs` , que é apropriado para um `onclick` evento de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="373e0-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="373e0-214">O `ParentComponent` define o filho <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) para seu `ShowMessage` método.</span><span class="sxs-lookup"><span data-stu-id="373e0-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="373e0-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="373e0-215">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="373e0-216">Quando o botão estiver selecionado no `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="373e0-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="373e0-217">O `ParentComponent` `ShowMessage` método é chamado.</span><span class="sxs-lookup"><span data-stu-id="373e0-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="373e0-218">`messageText` é atualizado e exibido no `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="373e0-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="373e0-219">Uma chamada para [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) não é necessária no método do retorno de chamada ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="373e0-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="373e0-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado automaticamente para renderizar novamente o `ParentComponent` , assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="373e0-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="373e0-221"><xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="373e0-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="373e0-222"><xref:Microsoft.AspNetCore.Components.EventCallback> é de tipo fraco e permite a passagem de qualquer argumento de tipo em `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="373e0-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="373e0-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> é fortemente tipado e requer a passagem de um `T` argumento no `InvokeAsync(T)` que seja atribuível ao `TValue` .</span><span class="sxs-lookup"><span data-stu-id="373e0-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="373e0-224">Invocar um <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> com <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e aguardar <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="373e0-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="373e0-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="373e0-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="373e0-226">Prefira o tipo fortemente tipado <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="373e0-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="373e0-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="373e0-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="373e0-228">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="373e0-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="373e0-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="373e0-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="373e0-230">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="373e0-230">Prevent default actions</span></span>

<span data-ttu-id="373e0-231">Use o [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributo diretiva para impedir a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="373e0-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="373e0-232">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="373e0-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="373e0-233">No exemplo a seguir, o comportamento padrão é impedido pela especificação do `@onkeypress:preventDefault` atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="373e0-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="373e0-234">O contador é incrementado e a **+** chave não é capturada no `<input>` valor do elemento:</span><span class="sxs-lookup"><span data-stu-id="373e0-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="373e0-235">Especificar o `@on{EVENT}:preventDefault` atributo sem um valor é equivalente a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="373e0-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="373e0-236">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="373e0-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="373e0-237">No exemplo a seguir, `shouldPreventDefault` é um `bool` conjunto de campos como `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="373e0-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="373e0-238">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="373e0-238">Stop event propagation</span></span>

<span data-ttu-id="373e0-239">Use o [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributo diretiva para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="373e0-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="373e0-240">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` se propaguem para o pai `<div>` :</span><span class="sxs-lookup"><span data-stu-id="373e0-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="373e0-241">Focalizar um elemento</span><span class="sxs-lookup"><span data-stu-id="373e0-241">Focus an element</span></span>

<span data-ttu-id="373e0-242">Chamada `FocusAsync` em uma [referência de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) para concentrar um elemento no código:</span><span class="sxs-lookup"><span data-stu-id="373e0-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
