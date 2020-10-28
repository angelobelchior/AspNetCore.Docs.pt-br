---
title: 'Chamar funções JavaScript de métodos .NET no ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Saiba como invocar funções JavaScript a partir de métodos .NET em :::no-loc(Blazor)::: aplicativos.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: ddbffa356a1cb53ee6ba1589f93e815af968bfb7
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690278"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="0f8e2-103">Chamar funções JavaScript de métodos .NET no ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="0f8e2-103">Call JavaScript functions from .NET methods in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="0f8e2-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0f8e2-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0f8e2-105">Um :::no-loc(Blazor)::: aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="0f8e2-106">Esses cenários são chamados de *interoperabilidade JavaScript* ( *operabilidade do js* ).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="0f8e2-107">Este artigo aborda a invocação de funções JavaScript do .NET.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="0f8e2-108">Para obter informações sobre como chamar métodos .NET do JavaScript, consulte <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="0f8e2-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f8e2-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0f8e2-110">Para chamar o JavaScript do .NET, use a <xref:Microsoft.JSInterop.IJSRuntime> abstração.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="0f8e2-111">Para emitir chamadas de interoperabilidade JS, insira a <xref:Microsoft.JSInterop.IJSRuntime> abstração em seu componente.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="0f8e2-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="0f8e2-113">O identificador de função é relativo ao escopo global ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="0f8e2-114">Se você quiser chamar `window.someScope.someFunction` , o identificador será `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="0f8e2-115">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="0f8e2-116">O tipo de retorno `T` também deve ser serializável em JSON.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="0f8e2-117">`T` deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="0f8e2-118">As funções JavaScript que retornam uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) são chamadas com <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="0f8e2-119">`InvokeAsync` desenvolve a promessa e retorna o valor esperado pela promessa.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="0f8e2-120">Para :::no-loc(Blazor Server)::: aplicativos com pré-processamento habilitado, não é possível chamar o JavaScript durante o pré-processamento inicial.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-120">For :::no-loc(Blazor Server)::: apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="0f8e2-121">As chamadas de interoperabilidade do JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="0f8e2-122">Para obter mais informações, consulte a seção [detectar quando um :::no-loc(Blazor Server)::: aplicativo está sendo renderizado](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-122">For more information, see the [Detect when a :::no-loc(Blazor Server)::: app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="0f8e2-123">O exemplo a seguir se baseia em [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) um decodificador baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="0f8e2-124">O exemplo demonstra como invocar uma função JavaScript de um método C# que descarrega um requisito do código do desenvolvedor para uma API JavaScript existente.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="0f8e2-125">A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="0f8e2-126">Dentro do `<head>` elemento de `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) ou `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-126">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="0f8e2-127">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( `.js` ) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="0f8e2-128">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-128">The following component:</span></span>

* <span data-ttu-id="0f8e2-129">Invoca a `convertArray` função JavaScript usando `JSRuntime` quando um botão de componente ( **`Convert Array`** ) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="0f8e2-130">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="0f8e2-131">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="0f8e2-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="0f8e2-132">IJSRuntime</span></span>

<span data-ttu-id="0f8e2-133">Para usar a <xref:Microsoft.JSInterop.IJSRuntime> abstração, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="0f8e2-134">Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração no :::no-loc(Razor)::: componente ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the :::no-loc(Razor)::: component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="0f8e2-135">Dentro do `<head>` elemento de `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) ou `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-135">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="0f8e2-136">A função é chamada com <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="0f8e2-137">Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração em uma classe ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="0f8e2-138">Dentro do `<head>` elemento de `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) ou `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-138">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="0f8e2-139">A função é chamada com `JSRuntime.InvokeAsync` e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="0f8e2-140">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use o `[Inject]` atributo:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="0f8e2-141">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="0f8e2-142">`showPrompt`: Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="0f8e2-143">`displayWelcome`: Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com um `id` de `welcome` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="0f8e2-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="0f8e2-145">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no `wwwroot/index.html` arquivo ( :::no-loc(Blazor WebAssembly)::: ) ou `Pages/_Host.cshtml` arquivo ( :::no-loc(Blazor Server)::: ).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::).</span></span>

<span data-ttu-id="0f8e2-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span></span>

[!code-html[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="0f8e2-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span></span>

[!code-cshtml[](./common/samples/3.x/:::no-loc(Blazor):::ServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="0f8e2-148">Não coloque uma `<script>` marca em um arquivo de componente porque a `<script>` marca não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="0f8e2-149">Os métodos .NET interoperam com as funções JavaScript no `exampleJsInterop.js` arquivo chamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0f8e2-150">A <xref:Microsoft.JSInterop.IJSRuntime> abstração é assíncrona para permitir :::no-loc(Blazor Server)::: cenários.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="0f8e2-151">Se o aplicativo for um :::no-loc(Blazor WebAssembly)::: aplicativo e você quiser invocar uma função JavaScript de forma síncrona, downcast <xref:Microsoft.JSInterop.IJSInProcessRuntime> e chame <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-151">If the app is a :::no-loc(Blazor WebAssembly)::: app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="0f8e2-152">Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0f8e2-153">Para habilitar o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão, consulte a seção [ :::no-loc(Blazor)::: isolamento de JavaScript e referências de objeto](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [:::no-loc(Blazor)::: JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="0f8e2-154">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="0f8e2-155">O componente:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-155">The component:</span></span>

* <span data-ttu-id="0f8e2-156">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="0f8e2-157">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="0f8e2-158">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="0f8e2-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to :::no-loc(Blazor):::!");
    }
}
```

<span data-ttu-id="0f8e2-160">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

1. <span data-ttu-id="0f8e2-161">Quando `TriggerJsPrompt` é executado selecionando o botão do componente **`Trigger JavaScript Prompt`** , a `showPrompt` função JavaScript fornecida no `wwwroot/exampleJsInterop.js` arquivo é chamada.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="0f8e2-162">A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="0f8e2-163">O componente armazena o nome do usuário em uma variável local, `name` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="0f8e2-164">A cadeia de caracteres armazenada em `name` é incorporada a uma mensagem de boas-vindas, que é passada para uma função JavaScript, `displayWelcome` , que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="0f8e2-165">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="0f8e2-165">Call a void JavaScript function</span></span>

<span data-ttu-id="0f8e2-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="0f8e2-167">Funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indefinido](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="0f8e2-168">Se o .NET não for necessário para ler o resultado de uma chamada JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="0f8e2-169">Detectar quando um :::no-loc(Blazor Server)::: aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="0f8e2-169">Detect when a :::no-loc(Blazor Server)::: app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="0f8e2-170">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="0f8e2-170">Capture references to elements</span></span>

<span data-ttu-id="0f8e2-171">Alguns cenários de interoperabilidade JS exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="0f8e2-172">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento, como `focus` ou `play` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="0f8e2-173">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="0f8e2-174">Adicione um `@ref` atributo ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="0f8e2-175">Defina um campo do tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cujo nome corresponde ao valor do `@ref` atributo.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="0f8e2-176">O exemplo a seguir mostra a captura de uma referência ao `username` `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="0f8e2-177">Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with :::no-loc(Blazor):::.</span></span> <span data-ttu-id="0f8e2-178">Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="0f8e2-179">Como o :::no-loc(Blazor)::: não interage com o elemento, não há possibilidade de um conflito entre a :::no-loc(Blazor)::: representação do elemento e o dom.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-179">Because :::no-loc(Blazor)::: doesn't interact with the element, there's no possibility of a conflict between :::no-loc(Blazor):::'s representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="0f8e2-180">No exemplo a seguir, é *perigoso* modificar o conteúdo da lista não ordenada ( `ul` ) porque :::no-loc(Blazor)::: interage com o dom para preencher os itens de lista deste elemento ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because :::no-loc(Blazor)::: interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="0f8e2-181">Se a interoperabilidade do JS converter o conteúdo do elemento `MyList` e :::no-loc(Blazor)::: tentar aplicar diffs ao elemento, as diferenças não corresponderão ao dom.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-181">If JS interop mutates the contents of element `MyList` and :::no-loc(Blazor)::: attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="0f8e2-182">Um <xref:Microsoft.AspNetCore.Components.ElementReference> é passado para o código JavaScript por meio da interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="0f8e2-183">O código JavaScript recebe uma `HTMLElement` instância, que pode ser usada com APIs dom normais.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="0f8e2-184">Por exemplo, o código a seguir define um método de extensão .NET que permite enviar um clique do mouse para um elemento:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="0f8e2-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0f8e2-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) em código C# para focalizar um elemento, que é interno da :::no-loc(Blazor)::: estrutura e funciona com referências de elemento.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the :::no-loc(Blazor)::: framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="0f8e2-187">Para chamar uma função JavaScript que não retorna um valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0f8e2-188">O código a seguir dispara um evento do lado `Click` do cliente chamando a função JavaScript anterior com o capturado <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="0f8e2-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="0f8e2-189">Para usar um método de extensão, crie um método de extensão estático que receba a <xref:Microsoft.JSInterop.IJSRuntime> instância:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="0f8e2-190">O `clickElement` método é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="0f8e2-191">O exemplo a seguir pressupõe que o `TriggerClickEvent` método está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="0f8e2-192">A `exampleButton` variável é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="0f8e2-193">Se um não populado <xref:Microsoft.AspNetCore.Components.ElementReference> for passado para o código JavaScript, o código JavaScript receberá um valor de `null` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="0f8e2-194">Para manipular referências de elemento após a conclusão da renderização do componente, use os [ `OnAfterRenderAsync` métodos de ciclo de vida do `OnAfterRender` componente ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="0f8e2-195">Ao trabalhar com tipos genéricos e retornar um valor, use <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="0f8e2-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="0f8e2-196">`GenericMethod` é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="0f8e2-197">O exemplo a seguir pressupõe que o `GenericMethod` está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="0f8e2-198">Elementos de referência entre componentes</span><span class="sxs-lookup"><span data-stu-id="0f8e2-198">Reference elements across components</span></span>

<span data-ttu-id="0f8e2-199">Um <xref:Microsoft.AspNetCore.Components.ElementReference> não pode ser passado entre componentes porque:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="0f8e2-200">Só é garantido que a instância exista depois que o componente é renderizado, que é durante ou após a execução do método de um componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="0f8e2-201">Um <xref:Microsoft.AspNetCore.Components.ElementReference> é um [`struct`](/csharp/language-reference/builtin-types/struct) , que não pode ser passado como um [parâmetro de componente](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="0f8e2-202">Para que um componente pai torne uma referência de elemento disponível para outros componentes, o componente pai pode:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="0f8e2-203">Permitir que componentes filho registrem retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="0f8e2-204">Invoque os retornos de chamada registrados durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento com a referência de elemento passada.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="0f8e2-205">Indiretamente, essa abordagem permite que os componentes filho interajam com a referência de elemento do pai.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="0f8e2-206">O exemplo a seguir :::no-loc(Blazor WebAssembly)::: ilustra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-206">The following :::no-loc(Blazor WebAssembly)::: example illustrates the approach.</span></span>

<span data-ttu-id="0f8e2-207">No `<head>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="0f8e2-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="0f8e2-208">No `<body>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="0f8e2-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="0f8e2-209">`Pages/Index.razor` (componente pai):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is :::no-loc(Blazor)::: working for you?" />
```

<span data-ttu-id="0f8e2-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-210">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="0f8e2-211">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="0f8e2-212">`Shared/SurveyPrompt.razor` (componente filho):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="0f8e2-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="0f8e2-214">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="0f8e2-215">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="0f8e2-215">Harden JS interop calls</span></span>

<span data-ttu-id="0f8e2-216">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="0f8e2-217">Por padrão, um :::no-loc(Blazor Server)::: aplicativo expira as chamadas de interoperabilidade js no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-217">By default, a :::no-loc(Blazor Server)::: app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="0f8e2-218">Se um aplicativo puder tolerar um tempo limite mais agressivo, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="0f8e2-219">Globalmente no `Startup.ConfigureServices` , especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSide:::no-loc(Blazor):::(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="0f8e2-220">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="0f8e2-221">Para obter mais informações sobre esgotamento de recursos, consulte <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="0f8e2-222">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="0f8e2-222">Avoid circular object references</span></span>

<span data-ttu-id="0f8e2-223">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="0f8e2-224">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-224">.NET method calls.</span></span>
* <span data-ttu-id="0f8e2-225">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="0f8e2-226">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="0f8e2-227">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="0f8e2-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="0f8e2-228">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="0f8e2-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="0f8e2-229">:::no-loc(Blazor)::: Isolamento de JavaScript e referências de objeto</span><span class="sxs-lookup"><span data-stu-id="0f8e2-229">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="0f8e2-230">:::no-loc(Blazor)::: habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-230">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="0f8e2-231">O isolamento de JavaScript oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="0f8e2-232">O JavaScript importado não polui mais o namespace global.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="0f8e2-233">Os consumidores de uma biblioteca e componentes não são necessários para importar o JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="0f8e2-234">Por exemplo, o módulo JavaScript a seguir exporta uma função JavaScript para mostrar um prompt do navegador:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="0f8e2-235">Adicione o módulo JavaScript anterior a uma biblioteca do .NET como um ativo da Web estático ( `wwwroot/exampleJsInterop.js` ) e, em seguida, importe o módulo para o código .NET usando o <xref:Microsoft.JSInterop.IJSRuntime> serviço.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="0f8e2-236">O serviço é injetado como `jsRuntime` (não mostrado) para o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-236">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="0f8e2-237">O `import` identificador no exemplo anterior é um identificador especial usado especificamente para importar um módulo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="0f8e2-238">Especifique o módulo usando seu caminho de ativo da Web estático estável: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="0f8e2-239">O espaço reservado `{LIBRARY NAME}` é o nome da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="0f8e2-240">O espaço reservado `{PATH UNDER WWWROOT}` é o caminho para o script em `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="0f8e2-241"><xref:Microsoft.JSInterop.IJSRuntime> importa o módulo como um `IJSObjectReference` , que representa uma referência a um objeto JavaScript do código .net.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="0f8e2-242">Use o `IJSObjectReference` para invocar funções JavaScript exportadas do módulo:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="0f8e2-243">`IJSInProcessObjectReference` representa uma referência a um objeto JavaScript cujas funções podem ser invocadas de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="0f8e2-244">`IJSUnmarshalledObjectReference` representa uma referência a um objeto JavaScript cujas funções podem ser invocadas sem a sobrecarga de serializar dados .NET.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-244">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="0f8e2-245">Isso pode ser usado em :::no-loc(Blazor WebAssembly)::: quando o desempenho é crucial:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-245">This can be used in :::no-loc(Blazor WebAssembly)::: when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = :::no-loc(Blazor):::.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="0f8e2-246">Uso de bibliotecas JavaScript que renderizam a interface do usuário (elementos DOM)</span><span class="sxs-lookup"><span data-stu-id="0f8e2-246">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="0f8e2-247">Às vezes, você pode querer usar bibliotecas JavaScript que produzem elementos visíveis da interface do usuário dentro do DOM do navegador.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-247">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="0f8e2-248">À primeira vista, isso pode parecer difícil, pois o :::no-loc(Blazor)::: sistema diferencial depende de ter controle sobre a árvore de elementos DOM e é executado em erros se algum código externo modifica a árvore DOM e invalida seu mecanismo para aplicar diffs.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-248">At first glance, this might seem difficult because :::no-loc(Blazor):::'s diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="0f8e2-249">Essa não é uma :::no-loc(Blazor)::: limitação específica.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-249">This isn't a :::no-loc(Blazor):::-specific limitation.</span></span> <span data-ttu-id="0f8e2-250">O mesmo desafio ocorre com qualquer estrutura de interface do usuário baseada em comparação.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-250">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="0f8e2-251">Felizmente, é simples inserir a interface do usuário gerada externamente em uma :::no-loc(Blazor)::: interface do usuário do componente de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-251">Fortunately, it's straightforward to embed externally-generated UI within a :::no-loc(Blazor)::: component UI reliably.</span></span> <span data-ttu-id="0f8e2-252">A técnica recomendada é fazer com que o código do componente ( `.razor` arquivo) produza um elemento vazio.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-252">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="0f8e2-253">No que diz :::no-loc(Blazor)::: respeito ao sistema diferencial, o elemento está sempre vazio, portanto, o renderizador não recursivamente no elemento e, em vez disso, deixa seu conteúdo sozinho.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-253">As far as :::no-loc(Blazor):::'s diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="0f8e2-254">Isso torna seguro popular o elemento com conteúdo gerenciado de modo externo arbitrário.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-254">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="0f8e2-255">O exemplo a seguir demonstra o conceito.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-255">The following example demonstrates the concept.</span></span> <span data-ttu-id="0f8e2-256">Na `if` instrução quando `firstRender` é `true` , faça algo com `myElement` .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-256">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="0f8e2-257">Por exemplo, chame uma biblioteca JavaScript externa para preenchê-la.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-257">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="0f8e2-258">:::no-loc(Blazor)::: Deixa o conteúdo do elemento sozinho até que este componente seja removido.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-258">:::no-loc(Blazor)::: leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="0f8e2-259">Quando o componente é removido, a subárvore DOM inteira do componente também é removida.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-259">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a :::no-loc(Blazor)::: component rendered at @DateTime.Now</h1>

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

<span data-ttu-id="0f8e2-260">Como um exemplo mais detalhado, considere o seguinte componente que renderiza um mapa interativo usando as [APIs de Mapbox de código aberto](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-260">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="0f8e2-261">O módulo JavaScript correspondente, que deve ser colocado em `wwwroot/mapComponent.js` , é o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-261">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="0f8e2-262">No exemplo anterior, substitua a cadeia de caracteres `{ACCESS TOKEN}` por um token de acesso válido do qual você pode obter https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-262">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="0f8e2-263">Para produzir o estilo correto, adicione a seguinte marca de folha de estilos à página HTML do host ( `index.html` ou `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-263">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="0f8e2-264">O exemplo anterior produz uma interface de usuário de mapa interativa, na qual o usuário:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-264">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="0f8e2-265">Pode arrastar para rolar ou aplicar zoom.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-265">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="0f8e2-266">Clique nos botões para saltar para locais predefinidos.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-266">Click buttons to jump to predefined locations.</span></span>

![Mapa de Mapbox de Tóquio, Japão com botões para selecionar Bristol, Reino Unido e Tóquio, Japão](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="0f8e2-268">Os pontos principais a serem compreendidos são:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-268">The key points to understand are:</span></span>

 * <span data-ttu-id="0f8e2-269">O `<div>` com o `@ref="mapElement"` é deixado vazio no que diz :::no-loc(Blazor)::: respeito.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-269">The `<div>` with `@ref="mapElement"` is left empty as far as :::no-loc(Blazor)::: is concerned.</span></span> <span data-ttu-id="0f8e2-270">Portanto, é seguro `mapbox-gl.js` preenchê-lo e modificar seu conteúdo ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-270">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="0f8e2-271">Você pode usar essa técnica com qualquer biblioteca JavaScript que renderiza a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-271">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="0f8e2-272">Você poderia até mesmo inserir componentes de uma estrutura de SPA do JavaScript de terceiros dentro de :::no-loc(Blazor)::: componentes, desde que eles não tentem acessar e modificar outras partes da página.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-272">You could even embed components from a third-party JavaScript SPA framework inside :::no-loc(Blazor)::: components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="0f8e2-273">*Não* é seguro que o código JavaScript externo modifique os elementos que não se :::no-loc(Blazor)::: consideram como vazios.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-273">It is *not* safe for external JavaScript code to modify elements that :::no-loc(Blazor)::: does not regard as empty.</span></span>
 * <span data-ttu-id="0f8e2-274">Ao usar essa abordagem, tenha em mente as regras sobre como o :::no-loc(Blazor)::: retém ou destrói elementos DOM.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-274">When using this approach, bear in mind the rules about how :::no-loc(Blazor)::: retains or destroys DOM elements.</span></span> <span data-ttu-id="0f8e2-275">No exemplo anterior, o componente manipula com segurança eventos de clique de botão e atualiza a instância de mapa existente porque os elementos DOM são retidos sempre que possível por padrão.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-275">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="0f8e2-276">Se você estivesse Renderizando uma lista de elementos de mapa de dentro de um `@foreach` loop, você deseja usar `@key` para garantir a preservação de instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-276">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="0f8e2-277">Caso contrário, as alterações nos dados da lista podem fazer com que as instâncias do componente retenham o estado das instâncias anteriores de maneira indesejável.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-277">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="0f8e2-278">Para obter mais informações, consulte [usando @key para preservar elementos e componentes](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-278">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="0f8e2-279">Além disso, o exemplo anterior mostra como é possível encapsular a lógica JavaScript e dependências dentro de um módulo ES6 e carregá-lo dinamicamente usando o `import` identificador.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-279">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="0f8e2-280">Para obter mais informações, consulte [isolamento de JavaScript e referências de objeto](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-280">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="0f8e2-281">Limites de tamanho em chamadas de interoperabilidade JS</span><span class="sxs-lookup"><span data-stu-id="0f8e2-281">Size limits on JS interop calls</span></span>

<span data-ttu-id="0f8e2-282">No :::no-loc(Blazor WebAssembly)::: , a estrutura não impõe limites para o tamanho das entradas e saídas de chamadas de interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-282">In :::no-loc(Blazor WebAssembly):::, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="0f8e2-283">No :::no-loc(Blazor Server)::: , o resultado de uma chamada de interoperabilidade js é limitado pelo tamanho máximo de carga imposto por :::no-loc(SignalR)::: ( <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> ), que usa como padrão 32 KB.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-283">In :::no-loc(Blazor Server):::, the result of a JS interop call is limited by the maximum payload size enforced by :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="0f8e2-284">Aplicativos que tentam responder a uma chamada de interoperabilidade JS com uma carga maior do que <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> gerar um erro.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-284">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="0f8e2-285">Um limite maior pode ser configurado modificando <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-285">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="0f8e2-286">O exemplo a seguir define o tamanho máximo da mensagem de recebimento como 64 KB (64 \* 1024 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="0f8e2-286">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSide:::no-loc(Blazor):::()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="0f8e2-287">Aumentar o :::no-loc(SignalR)::: limite acompanha o custo de exigir o uso de mais recursos de servidor e expõe o servidor para aumentar os riscos de um usuário mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-287">Increasing the :::no-loc(SignalR)::: limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="0f8e2-288">Além disso, a leitura de uma grande quantidade de conteúdo na memória como cadeias de caracteres ou matrizes de bytes também pode resultar em alocações que funcionam mal com o coletor de lixo, resultando em penalidades de desempenho adicionais.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-288">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="0f8e2-289">Uma opção para ler grandes cargas é considerar o envio do conteúdo em partes menores e o processamento da carga como um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="0f8e2-289">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="0f8e2-290">Isso pode ser usado ao ler cargas JSON grandes ou se os dados estiverem disponíveis em JavaScript como bytes brutos.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-290">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="0f8e2-291">Para obter um exemplo que demonstra o envio de grandes cargas binárias no :::no-loc(Blazor Server)::: que usa técnicas semelhantes ao `InputFile` componente, consulte o [aplicativo de exemplo enviar binário](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="0f8e2-291">For an example that demonstrates sending large binary payloads in :::no-loc(Blazor Server)::: that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="0f8e2-292">Considere as seguintes diretrizes ao desenvolver código que transfere uma grande quantidade de dados entre o JavaScript e o :::no-loc(Blazor)::: :</span><span class="sxs-lookup"><span data-stu-id="0f8e2-292">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and :::no-loc(Blazor)::::</span></span>

* <span data-ttu-id="0f8e2-293">Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-293">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="0f8e2-294">Não aloque objetos grandes em JavaScript e código C#.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-294">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="0f8e2-295">Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-295">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="0f8e2-296">Libere qualquer memória consumida quando o processo for concluído ou cancelado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-296">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="0f8e2-297">Impor os seguintes requisitos adicionais para fins de segurança:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-297">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="0f8e2-298">Declare o tamanho máximo de arquivo ou de dados que pode ser passado.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-298">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="0f8e2-299">Declare a taxa de carregamento mínima do cliente para o servidor.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-299">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="0f8e2-300">Depois que os dados são recebidos pelo servidor, os dados podem ser:</span><span class="sxs-lookup"><span data-stu-id="0f8e2-300">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="0f8e2-301">Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-301">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="0f8e2-302">Consumido imediatamente.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-302">Consumed immediately.</span></span> <span data-ttu-id="0f8e2-303">Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.</span><span class="sxs-lookup"><span data-stu-id="0f8e2-303">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0f8e2-304">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0f8e2-304">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="0f8e2-305">Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="0f8e2-305">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
