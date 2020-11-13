---
title: 'Chamar métodos .NET de funções JavaScript no ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Saiba como invocar métodos .NET de funções JavaScript em :::no-loc(Blazor)::: aplicativos.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: c4ab82490a2b16cdb5fef691cfb1ca038f1dca00
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570062"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="e770b-103">Chamar métodos .NET de funções JavaScript no ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="e770b-103">Call .NET methods from JavaScript functions in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="e770b-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e770b-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e770b-105">Um :::no-loc(Blazor)::: aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e770b-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="e770b-106">Esses cenários são chamados de *interoperabilidade JavaScript* ( *operabilidade do js* ).</span><span class="sxs-lookup"><span data-stu-id="e770b-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="e770b-107">Este artigo aborda a invocação de métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e770b-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="e770b-108">Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="e770b-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="e770b-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e770b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="e770b-110">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="e770b-110">Static .NET method call</span></span>

<span data-ttu-id="e770b-111">Para invocar um método .NET estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="e770b-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="e770b-112">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="e770b-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="e770b-113">A versão assíncrona é preferida para dar suporte a :::no-loc(Blazor Server)::: cenários.</span><span class="sxs-lookup"><span data-stu-id="e770b-113">The asynchronous version is preferred to support :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="e770b-114">O método .NET deve ser público, estático e ter o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="e770b-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="e770b-115">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="e770b-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="e770b-116">O aplicativo de exemplo inclui um método C# para retornar uma `int` matriz.</span><span class="sxs-lookup"><span data-stu-id="e770b-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="e770b-117">O [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="e770b-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="e770b-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="e770b-118">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e770b-119">O JavaScript servido para o cliente invoca o método .NET do C#.</span><span class="sxs-lookup"><span data-stu-id="e770b-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="e770b-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="e770b-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="e770b-121">Quando o **`Trigger .NET static method ReturnArrayAsync`** botão estiver selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="e770b-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="e770b-122">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="e770b-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="e770b-123">O quarto valor de matriz é enviado por push para a matriz ( `data.push(4);` ) retornada por `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="e770b-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="e770b-124">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) Construtor de atributos:</span><span class="sxs-lookup"><span data-stu-id="e770b-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e770b-125">No arquivo JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="e770b-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="e770b-126">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="e770b-127">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="e770b-127">Instance method call</span></span>

<span data-ttu-id="e770b-128">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e770b-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="e770b-129">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e770b-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="e770b-130">Passe a instância do .NET por referência ao JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e770b-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="e770b-131">Faça uma chamada estática para <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e770b-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="e770b-132">Empacote a instância em uma <xref:Microsoft.JSInterop.DotNetObjectReference> instância e chame <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> na <xref:Microsoft.JSInterop.DotNetObjectReference> instância.</span><span class="sxs-lookup"><span data-stu-id="e770b-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="e770b-133">Descarte de <xref:Microsoft.JSInterop.DotNetObjectReference> objetos (um exemplo aparece mais adiante nesta seção).</span><span class="sxs-lookup"><span data-stu-id="e770b-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="e770b-134">Invoque os métodos de instância do .NET na instância usando as `invokeMethod` `invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="e770b-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="e770b-135">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e770b-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="e770b-136">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="e770b-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="e770b-137">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="e770b-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="e770b-138">Quando o **`Trigger .NET instance method HelloHelper.SayHello`** botão é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `:::no-loc(Blazor):::` , para o método.</span><span class="sxs-lookup"><span data-stu-id="e770b-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `:::no-loc(Blazor):::`, to the method.</span></span>

<span data-ttu-id="e770b-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="e770b-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello(":::no-loc(Blazor):::");
    }
}
```

<span data-ttu-id="e770b-140">`CallHelloHelperSayHello` Invoca a função JavaScript `sayHello` com uma nova instância do `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="e770b-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="e770b-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="e770b-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="e770b-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="e770b-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="e770b-143">O nome é passado para o `HelloHelper` Construtor de, que define a `HelloHelper.Name` propriedade.</span><span class="sxs-lookup"><span data-stu-id="e770b-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="e770b-144">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e770b-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="e770b-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="e770b-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="e770b-146">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="e770b-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, :::no-loc(Blazor):::!
```

<span data-ttu-id="e770b-147">Para evitar um vazamento de memória e permitir a coleta de lixo em um componente que cria um <xref:Microsoft.JSInterop.DotNetObjectReference> , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e770b-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="e770b-148">Descarte o objeto na classe que criou a <xref:Microsoft.JSInterop.DotNetObjectReference> instância:</span><span class="sxs-lookup"><span data-stu-id="e770b-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="e770b-149">O padrão anterior mostrado na `ExampleJsInterop` classe também pode ser implementado em um componente:</span><span class="sxs-lookup"><span data-stu-id="e770b-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(":::no-loc(Blazor):::"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="e770b-150">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="e770b-151">Quando o componente ou classe não descartar o <xref:Microsoft.JSInterop.DotNetObjectReference> , descarte o objeto no cliente chamando `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="e770b-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="e770b-152">Chamada de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="e770b-152">Component instance method call</span></span>

<span data-ttu-id="e770b-153">Para invocar os métodos .NET de um componente:</span><span class="sxs-lookup"><span data-stu-id="e770b-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="e770b-154">Use a `invokeMethod` `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.</span><span class="sxs-lookup"><span data-stu-id="e770b-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="e770b-155">O método estático do componente encapsula a chamada para seu método de instância como uma chamada <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="e770b-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="e770b-156">Para :::no-loc(Blazor Server)::: aplicativos, em que vários usuários podem estar usando simultaneamente o mesmo componente, use uma classe auxiliar para invocar métodos de instância.</span><span class="sxs-lookup"><span data-stu-id="e770b-156">For :::no-loc(Blazor Server)::: apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="e770b-157">Para obter mais informações, consulte a seção [classe do método da instância do componente](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="e770b-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="e770b-158">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="e770b-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="e770b-159">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="e770b-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e770b-160">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="e770b-161">Para passar argumentos para o método de instância:</span><span class="sxs-lookup"><span data-stu-id="e770b-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="e770b-162">Adicione parâmetros à invocação do método JS.</span><span class="sxs-lookup"><span data-stu-id="e770b-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="e770b-163">No exemplo a seguir, um nome é passado para o método.</span><span class="sxs-lookup"><span data-stu-id="e770b-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="e770b-164">Parâmetros adicionais podem ser adicionados à lista, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="e770b-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="e770b-165">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="e770b-166">Forneça os tipos corretos para os <xref:System.Action> parâmetros.</span><span class="sxs-lookup"><span data-stu-id="e770b-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="e770b-167">Forneça a lista de parâmetros para os métodos C#.</span><span class="sxs-lookup"><span data-stu-id="e770b-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="e770b-168">Invoque o <xref:System.Action> ( `UpdateMessage` ) com os parâmetros ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="e770b-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e770b-169">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="e770b-170">Saída `message` quando o botão do **método de chamada js** for selecionado:</span><span class="sxs-lookup"><span data-stu-id="e770b-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="e770b-171">Classe auxiliar de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="e770b-171">Component instance method helper class</span></span>

<span data-ttu-id="e770b-172">A classe auxiliar é usada para invocar um método de instância como um <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="e770b-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="e770b-173">As classes auxiliares são úteis quando:</span><span class="sxs-lookup"><span data-stu-id="e770b-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="e770b-174">Vários componentes do mesmo tipo são renderizados na mesma página.</span><span class="sxs-lookup"><span data-stu-id="e770b-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="e770b-175">Um :::no-loc(Blazor Server)::: aplicativo é usado, onde vários usuários podem estar usando um componente simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="e770b-175">A :::no-loc(Blazor Server)::: app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="e770b-176">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e770b-176">In the following example:</span></span>

* <span data-ttu-id="e770b-177">O `JSInteropExample` componente contém vários `ListItem` componentes.</span><span class="sxs-lookup"><span data-stu-id="e770b-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="e770b-178">Cada `ListItem` componente é composto por uma mensagem e um botão.</span><span class="sxs-lookup"><span data-stu-id="e770b-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="e770b-179">Quando um `ListItem` botão de componente é selecionado, `ListItem` esse `UpdateMessage` método altera o texto do item da lista e oculta o botão.</span><span class="sxs-lookup"><span data-stu-id="e770b-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="e770b-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="e770b-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="e770b-181">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="e770b-182">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="e770b-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="e770b-183">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="e770b-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="e770b-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="e770b-184">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="e770b-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="e770b-185">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="e770b-186">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="e770b-186">Avoid circular object references</span></span>

<span data-ttu-id="e770b-187">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="e770b-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="e770b-188">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="e770b-188">.NET method calls.</span></span>
* <span data-ttu-id="e770b-189">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="e770b-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="e770b-190">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="e770b-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="e770b-191">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="e770b-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="e770b-192">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="e770b-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a><span data-ttu-id="e770b-193">Módulos JS</span><span class="sxs-lookup"><span data-stu-id="e770b-193">JS modules</span></span>

<span data-ttu-id="e770b-194">Para isolamento de JS, o JS Interop funciona com o suporte padrão do navegador para [módulos ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificação ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="e770b-194">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e770b-195">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e770b-195">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="e770b-196">`InteropComponent.razor` exemplo (dotNet/AspNetCore do repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="e770b-196">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
