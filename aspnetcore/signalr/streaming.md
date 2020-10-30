---
title: 'Usar Streaming no ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: Saiba como transmitir dados entre o cliente e o servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 10/29/2020
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
uid: signalr/streaming
ms.openlocfilehash: b07c280f271ccdd525128b973da065001a5cf0ed
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062435"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="08742-103">Usar Streaming no ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="08742-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="08742-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="08742-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08742-105">ASP.NET Core :::no-loc(SignalR)::: dá suporte ao streaming do cliente para o servidor e do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="08742-106">Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="08742-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="08742-107">Ao transmitir, cada fragmento é enviado ao cliente ou ao servidor assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="08742-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08742-108">O ASP.NET Core :::no-loc(SignalR)::: dá suporte a valores de retorno de streaming de métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="08742-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="08742-109">Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="08742-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="08742-110">Quando um valor de retorno é transmitido para o cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="08742-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="08742-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08742-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="08742-112">Configurar um hub para streaming</span><span class="sxs-lookup"><span data-stu-id="08742-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08742-113">Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` ou `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="08742-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08742-114">Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna um <xref:System.Threading.Channels.ChannelReader%601> ou um `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="08742-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="08742-115">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="08742-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08742-116">Os métodos do hub de streaming podem retornar além `IAsyncEnumerable<T>` do `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="08742-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="08742-117">A maneira mais simples de retornar `IAsyncEnumerable<T>` é fazendo com que o método de Hub seja um método de iterador assíncrono como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="08742-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="08742-118">Os métodos iteradores assíncronos de Hub podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="08742-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="08742-119">Os métodos iteradores assíncronos evitam problemas comuns com canais, como não retornar o `ChannelReader` início suficiente ou sair do método sem concluir o <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="08742-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="08742-120">O exemplo a seguir mostra as noções básicas de streaming de dados para o cliente usando canais.</span><span class="sxs-lookup"><span data-stu-id="08742-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="08742-121">Sempre que um objeto é gravado no <xref:System.Threading.Channels.ChannelWriter%601> , o objeto é enviado imediatamente para o cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="08742-122">No final, o `ChannelWriter` é concluído para informar ao cliente que o fluxo está fechado.</span><span class="sxs-lookup"><span data-stu-id="08742-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="08742-123">Grave em `ChannelWriter<T>` um thread em segundo plano e retorne o assim `ChannelReader` que possível.</span><span class="sxs-lookup"><span data-stu-id="08742-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="08742-124">Outras invocações de Hub são bloqueadas até que um `ChannelReader` seja retornado.</span><span class="sxs-lookup"><span data-stu-id="08742-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="08742-125">Ajustar a lógica em uma [ `try ... catch` instrução](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="08742-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="08742-126">Conclua o `Channel` em um [ `finally` bloco](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="08742-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="08742-127">Se você quiser fluir um erro, Capture-o dentro do `catch` bloco e grave-o no `finally` bloco.</span><span class="sxs-lookup"><span data-stu-id="08742-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="08742-128">Os métodos de Hub de streaming de servidor para cliente podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="08742-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="08742-129">Use esse token para interromper a operação do servidor e liberar todos os recursos se o cliente se desconectar antes do final do fluxo.</span><span class="sxs-lookup"><span data-stu-id="08742-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="08742-130">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="08742-130">Client-to-server streaming</span></span>

<span data-ttu-id="08742-131">Um método de Hub se torna automaticamente um método de Hub de streaming de cliente para servidor quando ele aceita um ou mais objetos do tipo <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="08742-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="08742-132">O exemplo a seguir mostra as noções básicas de leitura de dados de streaming enviados do cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="08742-133">Sempre que o cliente grava no <xref:System.Threading.Channels.ChannelWriter%601> , os dados são gravados no `ChannelReader` no servidor do qual o método de Hub está lendo.</span><span class="sxs-lookup"><span data-stu-id="08742-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="08742-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>Segue uma versão do método.</span><span class="sxs-lookup"><span data-stu-id="08742-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="08742-135">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="08742-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="08742-136">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="08742-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08742-137">Os `StreamAsync` `StreamAsChannelAsync` métodos e em `HubConnection` são usados para invocar métodos de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="08742-138">Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsync` ou `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="08742-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="08742-139">O parâmetro genérico em `StreamAsync<T>` e `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming.</span><span class="sxs-lookup"><span data-stu-id="08742-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="08742-140">Um objeto do tipo `IAsyncEnumerable<T>` ou `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="08742-141">Um `StreamAsync` exemplo que retorna `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="08742-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="08742-142">Um `StreamAsChannelAsync` exemplo correspondente que retorna `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="08742-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="08742-143">O `StreamAsChannelAsync` método on `HubConnection` é usado para invocar um método de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="08742-144">Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="08742-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="08742-145">O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming.</span><span class="sxs-lookup"><span data-stu-id="08742-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="08742-146">Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="08742-147">O `StreamAsChannelAsync` método on `HubConnection` é usado para invocar um método de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="08742-148">Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="08742-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="08742-149">O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming.</span><span class="sxs-lookup"><span data-stu-id="08742-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="08742-150">Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="08742-151">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="08742-151">Client-to-server streaming</span></span>

<span data-ttu-id="08742-152">Há duas maneiras de invocar um método de Hub de transmissão de cliente para servidor do cliente .NET.</span><span class="sxs-lookup"><span data-stu-id="08742-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="08742-153">Você pode passar um `IAsyncEnumerable<T>` ou um `ChannelReader` como um argumento para `SendAsync` , `InvokeAsync` ou `StreamAsChannelAsync` , dependendo do método de Hub invocado.</span><span class="sxs-lookup"><span data-stu-id="08742-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="08742-154">Sempre que os dados são gravados no `IAsyncEnumerable` `ChannelWriter` objeto ou, o método de Hub no servidor recebe um novo item com os dados do cliente.</span><span class="sxs-lookup"><span data-stu-id="08742-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="08742-155">Se estiver usando um `IAsyncEnumerable` objeto, o fluxo termina depois que o método que retorna os itens de fluxo é encerrado.</span><span class="sxs-lookup"><span data-stu-id="08742-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="08742-156">Ou, se você estiver usando um `ChannelWriter` , você conclui o canal com `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="08742-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="08742-157">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="08742-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="08742-158">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="08742-158">Server-to-client streaming</span></span>

<span data-ttu-id="08742-159">Os clientes JavaScript chamam métodos de streaming de servidor para cliente em hubs com `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="08742-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="08742-160">O `stream` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="08742-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="08742-161">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="08742-161">The name of the hub method.</span></span> <span data-ttu-id="08742-162">No exemplo a seguir, o nome do método de Hub é `Counter` .</span><span class="sxs-lookup"><span data-stu-id="08742-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="08742-163">Argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="08742-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="08742-164">No exemplo a seguir, os argumentos são uma contagem para o número de itens de fluxo a serem recebidos e o atraso entre itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="08742-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="08742-165">`connection.stream` Retorna um `IStreamResult` , que contém um `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="08742-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="08742-166">Passe um `IStreamSubscriber` para `subscribe` e defina os `next` `error` `complete` retornos de chamada, e para receber notificações da `stream` invocação.</span><span class="sxs-lookup"><span data-stu-id="08742-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="08742-167">Para finalizar o fluxo do cliente, chame o `dispose` método no `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="08742-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="08742-168">Chamar esse método causa o cancelamento do `CancellationToken` parâmetro do método de Hub, se você tiver fornecido um.</span><span class="sxs-lookup"><span data-stu-id="08742-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="08742-169">Para finalizar o fluxo do cliente, chame o `dispose` método no `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="08742-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="08742-170">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="08742-170">Client-to-server streaming</span></span>

<span data-ttu-id="08742-171">Os clientes JavaScript chamam métodos de transmissão de cliente para servidor em hubs, passando um `Subject` como um argumento para `send` , `invoke` ou `stream` , dependendo do método de Hub invocado.</span><span class="sxs-lookup"><span data-stu-id="08742-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="08742-172">O `Subject` é uma classe semelhante a um `Subject` .</span><span class="sxs-lookup"><span data-stu-id="08742-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="08742-173">Por exemplo, em RxJS, você pode usar a classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) dessa biblioteca.</span><span class="sxs-lookup"><span data-stu-id="08742-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="08742-174">Chamar `subject.next(item)` com um item grava o item no fluxo e o método Hub recebe o item no servidor.</span><span class="sxs-lookup"><span data-stu-id="08742-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="08742-175">Para encerrar o fluxo, chame `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="08742-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="08742-176">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="08742-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="08742-177">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="08742-177">Server-to-client streaming</span></span>

<span data-ttu-id="08742-178">O :::no-loc(SignalR)::: cliente Java usa o `stream` método para invocar métodos de streaming.</span><span class="sxs-lookup"><span data-stu-id="08742-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="08742-179">`stream` aceita três ou mais argumentos:</span><span class="sxs-lookup"><span data-stu-id="08742-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="08742-180">O tipo esperado dos itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="08742-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="08742-181">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="08742-181">The name of the hub method.</span></span>
* <span data-ttu-id="08742-182">Argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="08742-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="08742-183">O `stream` método em `HubConnection` retorna um observável do tipo de item de fluxo.</span><span class="sxs-lookup"><span data-stu-id="08742-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="08742-184">O método do tipo observável `subscribe` é `onNext` onde `onError` e os `onCompleted` manipuladores são definidos.</span><span class="sxs-lookup"><span data-stu-id="08742-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

### <a name="client-to-server-streaming"></a><span data-ttu-id="08742-185">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="08742-185">Client-to-server streaming</span></span>

<span data-ttu-id="08742-186">O :::no-loc(SignalR)::: cliente Java pode chamar métodos de streaming de cliente para servidor em hubs, passando um [observável](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) como um argumento para `send` , `invoke` ou `stream` , dependendo do método de Hub invocado.</span><span class="sxs-lookup"><span data-stu-id="08742-186">The :::no-loc(SignalR)::: Java client can call client-to-server streaming methods on hubs by passing in an [Observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span>

```java
ReplaySubject<String> stream = ReplaySubject.create();
hubConnection.send("UploadStream", stream);
stream.onNext("FirstItem");
stream.onNext("SecondItem");
stream.onComplete();
```

<span data-ttu-id="08742-187">Chamar `stream.onNext(item)` com um item grava o item no fluxo e o método Hub recebe o item no servidor.</span><span class="sxs-lookup"><span data-stu-id="08742-187">Calling `stream.onNext(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="08742-188">Para encerrar o fluxo, chame `stream.onComplete()` .</span><span class="sxs-lookup"><span data-stu-id="08742-188">To end the stream, call `stream.onComplete()`.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="08742-189">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="08742-189">Additional resources</span></span>

* [<span data-ttu-id="08742-190">Hubs</span><span class="sxs-lookup"><span data-stu-id="08742-190">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="08742-191">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="08742-191">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="08742-192">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="08742-192">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="08742-193">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="08742-193">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
