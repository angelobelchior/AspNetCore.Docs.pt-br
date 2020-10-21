---
title: Configuração de ASP.NET Core SignalR
author: bradygaster
description: Saiba como configurar os SignalR aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326587"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="a9f80-103">Configuração de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a9f80-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a9f80-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-105">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a9f80-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="a9f80-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a9f80-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a9f80-108">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9f80-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="a9f80-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a9f80-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="a9f80-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a9f80-111">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a9f80-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a9f80-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a9f80-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a9f80-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a9f80-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a9f80-116">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="a9f80-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a9f80-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a9f80-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a9f80-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-118">MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a9f80-120">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a9f80-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="a9f80-122">Configure server options</span></span>

<span data-ttu-id="a9f80-123">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="a9f80-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a9f80-124">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-124">Option</span></span> | <span data-ttu-id="a9f80-125">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-125">Default Value</span></span> | <span data-ttu-id="a9f80-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a9f80-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-127">30 seconds</span></span> | <span data-ttu-id="a9f80-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a9f80-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a9f80-130">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a9f80-131">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-131">15 seconds</span></span> | <span data-ttu-id="a9f80-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a9f80-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-134">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-135">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-135">15 seconds</span></span> | <span data-ttu-id="a9f80-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="a9f80-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a9f80-137">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a9f80-138">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a9f80-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="a9f80-139">All installed protocols</span></span> | <span data-ttu-id="a9f80-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a9f80-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a9f80-142">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a9f80-143">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a9f80-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a9f80-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a9f80-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-146">32 KB</span></span> | <span data-ttu-id="a9f80-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="a9f80-148">1</span><span class="sxs-lookup"><span data-stu-id="a9f80-148">1</span></span> | <span data-ttu-id="a9f80-149">O número máximo de métodos de Hub que cada cliente pode chamar em paralelo antes de enfileirar.</span><span class="sxs-lookup"><span data-stu-id="a9f80-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="a9f80-150">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a9f80-151">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a9f80-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a9f80-152">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="a9f80-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a9f80-153">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a9f80-154">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a9f80-155">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a9f80-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a9f80-156">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-156">Option</span></span> | <span data-ttu-id="a9f80-157">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-157">Default Value</span></span> | <span data-ttu-id="a9f80-158">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a9f80-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-159">32 KB</span></span> | <span data-ttu-id="a9f80-160">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="a9f80-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a9f80-161">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a9f80-162">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a9f80-163">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a9f80-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-164">32 KB</span></span> | <span data-ttu-id="a9f80-165">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="a9f80-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a9f80-166">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a9f80-167">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-167">All Transports are enabled.</span></span> | <span data-ttu-id="a9f80-168">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="a9f80-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a9f80-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-169">See below.</span></span> | <span data-ttu-id="a9f80-170">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a9f80-171">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-171">See below.</span></span> | <span data-ttu-id="a9f80-172">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a9f80-173">0</span><span class="sxs-lookup"><span data-stu-id="a9f80-173">0</span></span> | <span data-ttu-id="a9f80-174">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="a9f80-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a9f80-175">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a9f80-176">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a9f80-177">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-177">Option</span></span> | <span data-ttu-id="a9f80-178">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-178">Default Value</span></span> | <span data-ttu-id="a9f80-179">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a9f80-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-180">90 seconds</span></span> | <span data-ttu-id="a9f80-181">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a9f80-182">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="a9f80-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a9f80-183">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a9f80-184">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-184">Option</span></span> | <span data-ttu-id="a9f80-185">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-185">Default Value</span></span> | <span data-ttu-id="a9f80-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a9f80-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-187">5 seconds</span></span> | <span data-ttu-id="a9f80-188">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a9f80-189">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a9f80-190">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a9f80-191">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="a9f80-191">Configure client options</span></span>

<span data-ttu-id="a9f80-192">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a9f80-193">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="a9f80-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a9f80-194">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="a9f80-194">Configure logging</span></span>

<span data-ttu-id="a9f80-195">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a9f80-196">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a9f80-197">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-198">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="a9f80-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a9f80-199">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a9f80-200">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="a9f80-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a9f80-201">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a9f80-202">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="a9f80-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a9f80-203">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="a9f80-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a9f80-204">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9f80-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a9f80-205">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a9f80-206">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a9f80-207">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a9f80-207">The following table lists the available log levels.</span></span> <span data-ttu-id="a9f80-208">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a9f80-209">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a9f80-210">String</span><span class="sxs-lookup"><span data-stu-id="a9f80-210">String</span></span>                      | <span data-ttu-id="a9f80-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a9f80-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a9f80-212">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="a9f80-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a9f80-213">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="a9f80-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a9f80-214">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a9f80-215">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a9f80-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a9f80-216">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a9f80-217">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="a9f80-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a9f80-218">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a9f80-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a9f80-219">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="a9f80-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a9f80-220">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="a9f80-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a9f80-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="a9f80-221">Configure allowed transports</span></span>

<span data-ttu-id="a9f80-222">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a9f80-223">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a9f80-224">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-224">All transports are enabled by default.</span></span>

<span data-ttu-id="a9f80-225">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="a9f80-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a9f80-226">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a9f80-227">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="a9f80-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a9f80-228">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a9f80-229">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-230">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="a9f80-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a9f80-231">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="a9f80-231">Configure bearer authentication</span></span>

<span data-ttu-id="a9f80-232">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a9f80-233">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="a9f80-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a9f80-234">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a9f80-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a9f80-235">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a9f80-236">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a9f80-237">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a9f80-238">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a9f80-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a9f80-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a9f80-240">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a9f80-241">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a9f80-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a9f80-242">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="a9f80-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-243">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-244">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-244">Option</span></span> | <span data-ttu-id="a9f80-245">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-245">Default value</span></span> | <span data-ttu-id="a9f80-246">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a9f80-247">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-248">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-248">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-249">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-250">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-251">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a9f80-252">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-252">15 seconds</span></span> | <span data-ttu-id="a9f80-253">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-254">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-255">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-256">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-257">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-257">15 seconds</span></span> | <span data-ttu-id="a9f80-258">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-259">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-260">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a9f80-261">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="a9f80-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a9f80-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-263">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-263">Option</span></span> | <span data-ttu-id="a9f80-264">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-264">Default value</span></span> | <span data-ttu-id="a9f80-265">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a9f80-266">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-267">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-267">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-268">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a9f80-269">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-270">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a9f80-271">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-272">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-273">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-274">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-275">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-275">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-276">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-276">Option</span></span> | <span data-ttu-id="a9f80-277">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-277">Default value</span></span> | <span data-ttu-id="a9f80-278">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a9f80-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a9f80-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a9f80-280">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-281">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-281">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-282">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-283">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-284">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a9f80-285">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-285">15 seconds</span></span> | <span data-ttu-id="a9f80-286">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-287">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-288">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-289">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a9f80-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a9f80-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a9f80-291">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-292">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-293">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-294">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a9f80-295">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-295">Configure additional options</span></span>

<span data-ttu-id="a9f80-296">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="a9f80-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-297">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-298">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-298">.NET Option</span></span> |  <span data-ttu-id="a9f80-299">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-299">Default value</span></span> | <span data-ttu-id="a9f80-300">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a9f80-301">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a9f80-302">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-303">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-304">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a9f80-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-305">Empty</span></span> | <span data-ttu-id="a9f80-306">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a9f80-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-307">Empty</span></span> | <span data-ttu-id="a9f80-308">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a9f80-309">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-309">Empty</span></span> | <span data-ttu-id="a9f80-310">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a9f80-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-311">5 seconds</span></span> | <span data-ttu-id="a9f80-312">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-312">WebSockets only.</span></span> <span data-ttu-id="a9f80-313">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a9f80-314">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="a9f80-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a9f80-315">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-315">Empty</span></span> | <span data-ttu-id="a9f80-316">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a9f80-317">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a9f80-318">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="a9f80-319">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a9f80-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a9f80-320">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="a9f80-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a9f80-321">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="a9f80-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a9f80-322">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a9f80-323">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a9f80-324">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="a9f80-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a9f80-325">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a9f80-326">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="a9f80-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a9f80-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-328">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-328">JavaScript Option</span></span> | <span data-ttu-id="a9f80-329">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-329">Default Value</span></span> | <span data-ttu-id="a9f80-330">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a9f80-331">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a9f80-332">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="a9f80-333">Dicionário de cabeçalhos enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="a9f80-334">O envio de cabeçalhos no navegador não funciona para WebSockets ou <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> fluxo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a9f80-335">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a9f80-336">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-337">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-338">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="a9f80-339">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="a9f80-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="a9f80-340">Azure App serviço usa cookie s para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="a9f80-341">Para obter mais informações sobre o CORS com SignalR , consulte <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="a9f80-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-342">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-342">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-343">Opção Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-343">Java Option</span></span> | <span data-ttu-id="a9f80-344">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-344">Default Value</span></span> | <span data-ttu-id="a9f80-345">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a9f80-346">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a9f80-347">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-348">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-349">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a9f80-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a9f80-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a9f80-351">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-351">Empty</span></span> | <span data-ttu-id="a9f80-352">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a9f80-353">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a9f80-354">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="a9f80-355">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a9f80-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a9f80-356">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a9f80-357">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-358">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a9f80-359">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="a9f80-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a9f80-360">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a9f80-361">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9f80-362">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="a9f80-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a9f80-363">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="a9f80-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a9f80-364">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a9f80-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a9f80-365">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a9f80-366">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a9f80-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a9f80-367">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-368">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a9f80-369">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="a9f80-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a9f80-370">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a9f80-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a9f80-371">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-371">MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-372">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a9f80-373">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-374">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a9f80-375">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="a9f80-375">Configure server options</span></span>

<span data-ttu-id="a9f80-376">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="a9f80-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a9f80-377">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-377">Option</span></span> | <span data-ttu-id="a9f80-378">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-378">Default Value</span></span> | <span data-ttu-id="a9f80-379">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a9f80-380">30 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-380">30 seconds</span></span> | <span data-ttu-id="a9f80-381">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a9f80-382">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a9f80-383">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a9f80-384">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-384">15 seconds</span></span> | <span data-ttu-id="a9f80-385">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a9f80-386">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-387">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-388">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-388">15 seconds</span></span> | <span data-ttu-id="a9f80-389">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="a9f80-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a9f80-390">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a9f80-391">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a9f80-392">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="a9f80-392">All installed protocols</span></span> | <span data-ttu-id="a9f80-393">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-393">Protocols supported by this hub.</span></span> <span data-ttu-id="a9f80-394">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a9f80-395">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a9f80-396">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a9f80-397">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a9f80-398">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a9f80-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-399">32 KB</span></span> | <span data-ttu-id="a9f80-400">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a9f80-401">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a9f80-402">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a9f80-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a9f80-403">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="a9f80-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a9f80-404">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a9f80-405">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a9f80-406">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a9f80-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a9f80-407">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-407">Option</span></span> | <span data-ttu-id="a9f80-408">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-408">Default Value</span></span> | <span data-ttu-id="a9f80-409">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a9f80-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-410">32 KB</span></span> | <span data-ttu-id="a9f80-411">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="a9f80-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a9f80-412">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a9f80-413">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a9f80-414">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a9f80-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-415">32 KB</span></span> | <span data-ttu-id="a9f80-416">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="a9f80-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a9f80-417">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a9f80-418">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-418">All Transports are enabled.</span></span> | <span data-ttu-id="a9f80-419">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="a9f80-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a9f80-420">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-420">See below.</span></span> | <span data-ttu-id="a9f80-421">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a9f80-422">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-422">See below.</span></span> | <span data-ttu-id="a9f80-423">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a9f80-424">0</span><span class="sxs-lookup"><span data-stu-id="a9f80-424">0</span></span> | <span data-ttu-id="a9f80-425">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="a9f80-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a9f80-426">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a9f80-427">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a9f80-428">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-428">Option</span></span> | <span data-ttu-id="a9f80-429">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-429">Default Value</span></span> | <span data-ttu-id="a9f80-430">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a9f80-431">90 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-431">90 seconds</span></span> | <span data-ttu-id="a9f80-432">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a9f80-433">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="a9f80-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a9f80-434">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a9f80-435">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-435">Option</span></span> | <span data-ttu-id="a9f80-436">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-436">Default Value</span></span> | <span data-ttu-id="a9f80-437">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a9f80-438">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-438">5 seconds</span></span> | <span data-ttu-id="a9f80-439">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a9f80-440">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a9f80-441">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a9f80-442">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="a9f80-442">Configure client options</span></span>

<span data-ttu-id="a9f80-443">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a9f80-444">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="a9f80-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a9f80-445">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="a9f80-445">Configure logging</span></span>

<span data-ttu-id="a9f80-446">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a9f80-447">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a9f80-448">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-449">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="a9f80-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a9f80-450">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a9f80-451">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="a9f80-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a9f80-452">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a9f80-453">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="a9f80-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a9f80-454">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="a9f80-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a9f80-455">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9f80-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a9f80-456">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a9f80-457">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a9f80-458">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a9f80-458">The following table lists the available log levels.</span></span> <span data-ttu-id="a9f80-459">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a9f80-460">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a9f80-461">String</span><span class="sxs-lookup"><span data-stu-id="a9f80-461">String</span></span>                      | <span data-ttu-id="a9f80-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a9f80-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a9f80-463">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="a9f80-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a9f80-464">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="a9f80-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a9f80-465">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a9f80-466">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a9f80-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a9f80-467">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a9f80-468">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="a9f80-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a9f80-469">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a9f80-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a9f80-470">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="a9f80-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a9f80-471">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="a9f80-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a9f80-472">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="a9f80-472">Configure allowed transports</span></span>

<span data-ttu-id="a9f80-473">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a9f80-474">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a9f80-475">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-475">All transports are enabled by default.</span></span>

<span data-ttu-id="a9f80-476">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="a9f80-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a9f80-477">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a9f80-478">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="a9f80-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a9f80-479">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a9f80-480">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-481">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="a9f80-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a9f80-482">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="a9f80-482">Configure bearer authentication</span></span>

<span data-ttu-id="a9f80-483">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a9f80-484">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="a9f80-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a9f80-485">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a9f80-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a9f80-486">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a9f80-487">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a9f80-488">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a9f80-489">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a9f80-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a9f80-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a9f80-491">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a9f80-492">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a9f80-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a9f80-493">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="a9f80-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-494">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-495">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-495">Option</span></span> | <span data-ttu-id="a9f80-496">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-496">Default value</span></span> | <span data-ttu-id="a9f80-497">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a9f80-498">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-499">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-499">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-500">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-501">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-502">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a9f80-503">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-503">15 seconds</span></span> | <span data-ttu-id="a9f80-504">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-505">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-506">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-507">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-508">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-508">15 seconds</span></span> | <span data-ttu-id="a9f80-509">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-510">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-511">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a9f80-512">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="a9f80-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a9f80-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-514">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-514">Option</span></span> | <span data-ttu-id="a9f80-515">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-515">Default value</span></span> | <span data-ttu-id="a9f80-516">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a9f80-517">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-518">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-518">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-519">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a9f80-520">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-521">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a9f80-522">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-523">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-524">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-525">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-526">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-526">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-527">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-527">Option</span></span> | <span data-ttu-id="a9f80-528">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-528">Default value</span></span> | <span data-ttu-id="a9f80-529">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a9f80-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a9f80-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a9f80-531">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-532">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-532">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-533">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-534">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-535">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a9f80-536">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-536">15 seconds</span></span> | <span data-ttu-id="a9f80-537">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-538">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-539">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-540">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a9f80-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a9f80-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a9f80-542">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-543">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-544">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-545">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a9f80-546">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-546">Configure additional options</span></span>

<span data-ttu-id="a9f80-547">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="a9f80-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-548">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-549">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-549">.NET Option</span></span> |  <span data-ttu-id="a9f80-550">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-550">Default value</span></span> | <span data-ttu-id="a9f80-551">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a9f80-552">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a9f80-553">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-554">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-555">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a9f80-556">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-556">Empty</span></span> | <span data-ttu-id="a9f80-557">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a9f80-558">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-558">Empty</span></span> | <span data-ttu-id="a9f80-559">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a9f80-560">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-560">Empty</span></span> | <span data-ttu-id="a9f80-561">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a9f80-562">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-562">5 seconds</span></span> | <span data-ttu-id="a9f80-563">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-563">WebSockets only.</span></span> <span data-ttu-id="a9f80-564">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a9f80-565">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="a9f80-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a9f80-566">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-566">Empty</span></span> | <span data-ttu-id="a9f80-567">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a9f80-568">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a9f80-569">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="a9f80-570">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a9f80-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a9f80-571">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="a9f80-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a9f80-572">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="a9f80-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a9f80-573">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a9f80-574">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a9f80-575">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="a9f80-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a9f80-576">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a9f80-577">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="a9f80-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a9f80-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-579">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-579">JavaScript Option</span></span> | <span data-ttu-id="a9f80-580">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-580">Default Value</span></span> | <span data-ttu-id="a9f80-581">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a9f80-582">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a9f80-583">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a9f80-584">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a9f80-585">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-586">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-587">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-588">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-588">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-589">Opção Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-589">Java Option</span></span> | <span data-ttu-id="a9f80-590">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-590">Default Value</span></span> | <span data-ttu-id="a9f80-591">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a9f80-592">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a9f80-593">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-594">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-595">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a9f80-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a9f80-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a9f80-597">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-597">Empty</span></span> | <span data-ttu-id="a9f80-598">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a9f80-599">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a9f80-600">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a9f80-601">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a9f80-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a9f80-602">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a9f80-603">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-604">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a9f80-605">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="a9f80-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a9f80-606">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a9f80-607">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9f80-608">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="a9f80-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a9f80-609">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="a9f80-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a9f80-610">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a9f80-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a9f80-611">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="a9f80-612">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a9f80-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a9f80-613">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-614">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a9f80-615">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="a9f80-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a9f80-616">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a9f80-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a9f80-617">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-617">MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-618">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a9f80-619">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-620">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a9f80-621">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="a9f80-621">Configure server options</span></span>

<span data-ttu-id="a9f80-622">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="a9f80-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a9f80-623">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-623">Option</span></span> | <span data-ttu-id="a9f80-624">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-624">Default Value</span></span> | <span data-ttu-id="a9f80-625">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a9f80-626">30 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-626">30 seconds</span></span> | <span data-ttu-id="a9f80-627">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a9f80-628">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a9f80-629">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a9f80-630">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-630">15 seconds</span></span> | <span data-ttu-id="a9f80-631">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a9f80-632">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-633">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-634">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-634">15 seconds</span></span> | <span data-ttu-id="a9f80-635">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="a9f80-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a9f80-636">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a9f80-637">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a9f80-638">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="a9f80-638">All installed protocols</span></span> | <span data-ttu-id="a9f80-639">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-639">Protocols supported by this hub.</span></span> <span data-ttu-id="a9f80-640">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a9f80-641">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a9f80-642">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a9f80-643">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a9f80-644">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a9f80-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-645">32 KB</span></span> | <span data-ttu-id="a9f80-646">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a9f80-647">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a9f80-648">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a9f80-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a9f80-649">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="a9f80-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a9f80-650">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a9f80-651">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a9f80-652">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a9f80-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a9f80-653">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-653">Option</span></span> | <span data-ttu-id="a9f80-654">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-654">Default Value</span></span> | <span data-ttu-id="a9f80-655">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a9f80-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-656">32 KB</span></span> | <span data-ttu-id="a9f80-657">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="a9f80-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a9f80-658">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a9f80-659">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a9f80-660">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a9f80-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-661">32 KB</span></span> | <span data-ttu-id="a9f80-662">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="a9f80-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a9f80-663">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a9f80-664">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-664">All Transports are enabled.</span></span> | <span data-ttu-id="a9f80-665">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="a9f80-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a9f80-666">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-666">See below.</span></span> | <span data-ttu-id="a9f80-667">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a9f80-668">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-668">See below.</span></span> | <span data-ttu-id="a9f80-669">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a9f80-670">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a9f80-671">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-671">Option</span></span> | <span data-ttu-id="a9f80-672">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-672">Default Value</span></span> | <span data-ttu-id="a9f80-673">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a9f80-674">90 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-674">90 seconds</span></span> | <span data-ttu-id="a9f80-675">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a9f80-676">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="a9f80-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a9f80-677">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a9f80-678">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-678">Option</span></span> | <span data-ttu-id="a9f80-679">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-679">Default Value</span></span> | <span data-ttu-id="a9f80-680">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a9f80-681">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-681">5 seconds</span></span> | <span data-ttu-id="a9f80-682">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a9f80-683">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a9f80-684">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a9f80-685">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="a9f80-685">Configure client options</span></span>

<span data-ttu-id="a9f80-686">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a9f80-687">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="a9f80-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a9f80-688">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="a9f80-688">Configure logging</span></span>

<span data-ttu-id="a9f80-689">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a9f80-690">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a9f80-691">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-692">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="a9f80-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a9f80-693">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a9f80-694">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="a9f80-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a9f80-695">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a9f80-696">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="a9f80-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a9f80-697">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="a9f80-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a9f80-698">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9f80-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a9f80-699">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a9f80-700">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a9f80-701">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a9f80-701">The following table lists the available log levels.</span></span> <span data-ttu-id="a9f80-702">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a9f80-703">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a9f80-704">String</span><span class="sxs-lookup"><span data-stu-id="a9f80-704">String</span></span>                      | <span data-ttu-id="a9f80-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a9f80-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a9f80-706">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="a9f80-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a9f80-707">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="a9f80-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a9f80-708">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a9f80-709">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a9f80-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a9f80-710">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a9f80-711">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="a9f80-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a9f80-712">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a9f80-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a9f80-713">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="a9f80-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a9f80-714">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="a9f80-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a9f80-715">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="a9f80-715">Configure allowed transports</span></span>

<span data-ttu-id="a9f80-716">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a9f80-717">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a9f80-718">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-718">All transports are enabled by default.</span></span>

<span data-ttu-id="a9f80-719">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="a9f80-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a9f80-720">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a9f80-721">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="a9f80-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a9f80-722">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a9f80-723">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-724">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="a9f80-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a9f80-725">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="a9f80-725">Configure bearer authentication</span></span>

<span data-ttu-id="a9f80-726">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a9f80-727">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="a9f80-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a9f80-728">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a9f80-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a9f80-729">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a9f80-730">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a9f80-731">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a9f80-732">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a9f80-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a9f80-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a9f80-734">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a9f80-735">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a9f80-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a9f80-736">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="a9f80-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-737">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-738">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-738">Option</span></span> | <span data-ttu-id="a9f80-739">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-739">Default value</span></span> | <span data-ttu-id="a9f80-740">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a9f80-741">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-742">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-742">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-743">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-744">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-745">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a9f80-746">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-746">15 seconds</span></span> | <span data-ttu-id="a9f80-747">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-748">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-749">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-750">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-751">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-751">15 seconds</span></span> | <span data-ttu-id="a9f80-752">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-753">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-754">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a9f80-755">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="a9f80-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a9f80-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-757">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-757">Option</span></span> | <span data-ttu-id="a9f80-758">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-758">Default value</span></span> | <span data-ttu-id="a9f80-759">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a9f80-760">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-761">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-761">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-762">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a9f80-763">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-764">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a9f80-765">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-766">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-767">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-768">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-769">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-769">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-770">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-770">Option</span></span> | <span data-ttu-id="a9f80-771">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-771">Default value</span></span> | <span data-ttu-id="a9f80-772">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a9f80-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a9f80-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a9f80-774">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-775">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-775">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-776">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-777">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-778">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a9f80-779">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-779">15 seconds</span></span> | <span data-ttu-id="a9f80-780">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-781">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-782">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-783">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a9f80-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a9f80-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a9f80-785">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-786">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-787">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-788">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a9f80-789">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-789">Configure additional options</span></span>

<span data-ttu-id="a9f80-790">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="a9f80-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-791">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-792">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-792">.NET Option</span></span> |  <span data-ttu-id="a9f80-793">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-793">Default value</span></span> | <span data-ttu-id="a9f80-794">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a9f80-795">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a9f80-796">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-797">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-798">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a9f80-799">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-799">Empty</span></span> | <span data-ttu-id="a9f80-800">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a9f80-801">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-801">Empty</span></span> | <span data-ttu-id="a9f80-802">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a9f80-803">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-803">Empty</span></span> | <span data-ttu-id="a9f80-804">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a9f80-805">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-805">5 seconds</span></span> | <span data-ttu-id="a9f80-806">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-806">WebSockets only.</span></span> <span data-ttu-id="a9f80-807">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a9f80-808">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="a9f80-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a9f80-809">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-809">Empty</span></span> | <span data-ttu-id="a9f80-810">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a9f80-811">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a9f80-812">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="a9f80-813">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a9f80-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a9f80-814">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="a9f80-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a9f80-815">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="a9f80-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a9f80-816">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a9f80-817">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a9f80-818">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="a9f80-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a9f80-819">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a9f80-820">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="a9f80-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a9f80-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-822">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-822">JavaScript Option</span></span> | <span data-ttu-id="a9f80-823">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-823">Default Value</span></span> | <span data-ttu-id="a9f80-824">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a9f80-825">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a9f80-826">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a9f80-827">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a9f80-828">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-829">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-830">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-831">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-831">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-832">Opção Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-832">Java Option</span></span> | <span data-ttu-id="a9f80-833">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-833">Default Value</span></span> | <span data-ttu-id="a9f80-834">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a9f80-835">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a9f80-836">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-837">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-838">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a9f80-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a9f80-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a9f80-840">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-840">Empty</span></span> | <span data-ttu-id="a9f80-841">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a9f80-842">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a9f80-843">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a9f80-844">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a9f80-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a9f80-845">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a9f80-846">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-847">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a9f80-848">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="a9f80-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a9f80-849">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a9f80-850">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="a9f80-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a9f80-851">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="a9f80-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a9f80-852">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a9f80-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a9f80-853">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a9f80-854">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a9f80-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a9f80-855">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-856">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a9f80-857">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-857">MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-858">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a9f80-859">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-860">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a9f80-861">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="a9f80-861">Configure server options</span></span>

<span data-ttu-id="a9f80-862">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="a9f80-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a9f80-863">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-863">Option</span></span> | <span data-ttu-id="a9f80-864">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-864">Default Value</span></span> | <span data-ttu-id="a9f80-865">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a9f80-866">30 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-866">30 seconds</span></span> | <span data-ttu-id="a9f80-867">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a9f80-868">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a9f80-869">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a9f80-870">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-870">15 seconds</span></span> | <span data-ttu-id="a9f80-871">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a9f80-872">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-873">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-874">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-874">15 seconds</span></span> | <span data-ttu-id="a9f80-875">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="a9f80-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a9f80-876">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a9f80-877">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a9f80-878">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="a9f80-878">All installed protocols</span></span> | <span data-ttu-id="a9f80-879">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-879">Protocols supported by this hub.</span></span> <span data-ttu-id="a9f80-880">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a9f80-881">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a9f80-882">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a9f80-883">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a9f80-884">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a9f80-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a9f80-885">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="a9f80-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a9f80-886">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a9f80-887">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="a9f80-888">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a9f80-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a9f80-889">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-889">Option</span></span> | <span data-ttu-id="a9f80-890">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-890">Default Value</span></span> | <span data-ttu-id="a9f80-891">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a9f80-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-892">32 KB</span></span> | <span data-ttu-id="a9f80-893">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="a9f80-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a9f80-894">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a9f80-895">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a9f80-896">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a9f80-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-897">32 KB</span></span> | <span data-ttu-id="a9f80-898">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="a9f80-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a9f80-899">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a9f80-900">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-900">All Transports are enabled.</span></span> | <span data-ttu-id="a9f80-901">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="a9f80-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a9f80-902">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-902">See below.</span></span> | <span data-ttu-id="a9f80-903">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a9f80-904">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-904">See below.</span></span> | <span data-ttu-id="a9f80-905">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a9f80-906">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a9f80-907">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-907">Option</span></span> | <span data-ttu-id="a9f80-908">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-908">Default Value</span></span> | <span data-ttu-id="a9f80-909">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a9f80-910">90 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-910">90 seconds</span></span> | <span data-ttu-id="a9f80-911">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a9f80-912">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="a9f80-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a9f80-913">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a9f80-914">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-914">Option</span></span> | <span data-ttu-id="a9f80-915">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-915">Default Value</span></span> | <span data-ttu-id="a9f80-916">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a9f80-917">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-917">5 seconds</span></span> | <span data-ttu-id="a9f80-918">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a9f80-919">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a9f80-920">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a9f80-921">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="a9f80-921">Configure client options</span></span>

<span data-ttu-id="a9f80-922">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a9f80-923">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="a9f80-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a9f80-924">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="a9f80-924">Configure logging</span></span>

<span data-ttu-id="a9f80-925">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a9f80-926">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a9f80-927">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-928">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="a9f80-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a9f80-929">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a9f80-930">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="a9f80-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a9f80-931">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a9f80-932">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="a9f80-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a9f80-933">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="a9f80-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a9f80-934">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9f80-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-935">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a9f80-936">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a9f80-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a9f80-937">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a9f80-938">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="a9f80-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a9f80-939">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a9f80-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a9f80-940">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="a9f80-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a9f80-941">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="a9f80-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a9f80-942">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="a9f80-942">Configure allowed transports</span></span>

<span data-ttu-id="a9f80-943">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a9f80-944">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a9f80-945">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-945">All transports are enabled by default.</span></span>

<span data-ttu-id="a9f80-946">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="a9f80-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a9f80-947">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a9f80-948">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="a9f80-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a9f80-949">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="a9f80-949">Configure bearer authentication</span></span>

<span data-ttu-id="a9f80-950">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a9f80-951">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="a9f80-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a9f80-952">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a9f80-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a9f80-953">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a9f80-954">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a9f80-955">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a9f80-956">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a9f80-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a9f80-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a9f80-958">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a9f80-959">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a9f80-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a9f80-960">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="a9f80-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-961">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-962">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-962">Option</span></span> | <span data-ttu-id="a9f80-963">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-963">Default value</span></span> | <span data-ttu-id="a9f80-964">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a9f80-965">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-966">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-966">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-967">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-968">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-969">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a9f80-970">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-970">15 seconds</span></span> | <span data-ttu-id="a9f80-971">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-972">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-973">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-974">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-975">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-975">15 seconds</span></span> | <span data-ttu-id="a9f80-976">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-977">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-978">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a9f80-979">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="a9f80-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a9f80-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-981">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-981">Option</span></span> | <span data-ttu-id="a9f80-982">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-982">Default value</span></span> | <span data-ttu-id="a9f80-983">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a9f80-984">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-985">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-985">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-986">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a9f80-987">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-988">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a9f80-989">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-990">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-991">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-992">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-993">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-993">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-994">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-994">Option</span></span> | <span data-ttu-id="a9f80-995">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-995">Default value</span></span> | <span data-ttu-id="a9f80-996">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a9f80-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a9f80-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a9f80-998">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-999">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-999">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-1000">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-1001">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-1002">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a9f80-1003">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-1003">15 seconds</span></span> | <span data-ttu-id="a9f80-1004">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-1005">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-1006">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-1007">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a9f80-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a9f80-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a9f80-1009">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-1010">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a9f80-1011">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a9f80-1012">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a9f80-1013">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-1013">Configure additional options</span></span>

<span data-ttu-id="a9f80-1014">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-1016">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-1016">.NET Option</span></span> |  <span data-ttu-id="a9f80-1017">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1017">Default value</span></span> | <span data-ttu-id="a9f80-1018">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a9f80-1019">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a9f80-1020">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-1021">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-1022">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a9f80-1023">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1023">Empty</span></span> | <span data-ttu-id="a9f80-1024">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a9f80-1025">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1025">Empty</span></span> | <span data-ttu-id="a9f80-1026">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a9f80-1027">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1027">Empty</span></span> | <span data-ttu-id="a9f80-1028">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a9f80-1029">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-1029">5 seconds</span></span> | <span data-ttu-id="a9f80-1030">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1030">WebSockets only.</span></span> <span data-ttu-id="a9f80-1031">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a9f80-1032">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a9f80-1033">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1033">Empty</span></span> | <span data-ttu-id="a9f80-1034">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a9f80-1035">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a9f80-1036">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="a9f80-1037">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a9f80-1038">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a9f80-1039">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="a9f80-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a9f80-1040">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a9f80-1041">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a9f80-1042">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a9f80-1043">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a9f80-1044">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a9f80-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-1046">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-1046">JavaScript Option</span></span> | <span data-ttu-id="a9f80-1047">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1047">Default Value</span></span> | <span data-ttu-id="a9f80-1048">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a9f80-1049">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a9f80-1050">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a9f80-1051">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a9f80-1052">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-1053">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-1054">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-1055">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-1056">Opção Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-1056">Java Option</span></span> | <span data-ttu-id="a9f80-1057">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1057">Default Value</span></span> | <span data-ttu-id="a9f80-1058">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a9f80-1059">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a9f80-1060">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-1061">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-1062">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a9f80-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a9f80-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a9f80-1064">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1064">Empty</span></span> | <span data-ttu-id="a9f80-1065">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a9f80-1066">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a9f80-1067">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a9f80-1068">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a9f80-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a9f80-1069">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a9f80-1070">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-1071">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a9f80-1072">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a9f80-1073">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a9f80-1074">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a9f80-1075">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a9f80-1076">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a9f80-1077">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a9f80-1078">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a9f80-1079">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-1080">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a9f80-1081">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="a9f80-1081">MessagePack serialization options</span></span>

<span data-ttu-id="a9f80-1082">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a9f80-1083">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-1084">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a9f80-1085">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="a9f80-1085">Configure server options</span></span>

<span data-ttu-id="a9f80-1086">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a9f80-1087">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1087">Option</span></span> | <span data-ttu-id="a9f80-1088">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1088">Default Value</span></span> | <span data-ttu-id="a9f80-1089">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a9f80-1090">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-1090">15 seconds</span></span> | <span data-ttu-id="a9f80-1091">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a9f80-1092">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-1093">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a9f80-1094">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-1094">15 seconds</span></span> | <span data-ttu-id="a9f80-1095">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a9f80-1096">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a9f80-1097">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a9f80-1098">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="a9f80-1098">All installed protocols</span></span> | <span data-ttu-id="a9f80-1099">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="a9f80-1100">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a9f80-1101">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a9f80-1102">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a9f80-1103">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a9f80-1104">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a9f80-1105">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="a9f80-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a9f80-1106">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a9f80-1107">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="a9f80-1108">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a9f80-1109">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1109">Option</span></span> | <span data-ttu-id="a9f80-1110">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1110">Default Value</span></span> | <span data-ttu-id="a9f80-1111">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a9f80-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-1112">32 KB</span></span> | <span data-ttu-id="a9f80-1113">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a9f80-1114">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a9f80-1115">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a9f80-1116">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a9f80-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9f80-1117">32 KB</span></span> | <span data-ttu-id="a9f80-1118">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a9f80-1119">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a9f80-1120">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1120">All Transports are enabled.</span></span> | <span data-ttu-id="a9f80-1121">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a9f80-1122">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1122">See below.</span></span> | <span data-ttu-id="a9f80-1123">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a9f80-1124">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1124">See below.</span></span> | <span data-ttu-id="a9f80-1125">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a9f80-1126">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a9f80-1127">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1127">Option</span></span> | <span data-ttu-id="a9f80-1128">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1128">Default Value</span></span> | <span data-ttu-id="a9f80-1129">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a9f80-1130">90 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-1130">90 seconds</span></span> | <span data-ttu-id="a9f80-1131">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a9f80-1132">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a9f80-1133">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a9f80-1134">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1134">Option</span></span> | <span data-ttu-id="a9f80-1135">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1135">Default Value</span></span> | <span data-ttu-id="a9f80-1136">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a9f80-1137">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-1137">5 seconds</span></span> | <span data-ttu-id="a9f80-1138">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a9f80-1139">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a9f80-1140">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a9f80-1141">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="a9f80-1141">Configure client options</span></span>

<span data-ttu-id="a9f80-1142">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a9f80-1143">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a9f80-1144">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="a9f80-1144">Configure logging</span></span>

<span data-ttu-id="a9f80-1145">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a9f80-1146">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a9f80-1147">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a9f80-1148">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a9f80-1149">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a9f80-1150">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a9f80-1151">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a9f80-1152">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a9f80-1153">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a9f80-1154">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a9f80-1155">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a9f80-1156">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a9f80-1157">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a9f80-1158">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a9f80-1159">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a9f80-1160">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a9f80-1161">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a9f80-1162">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="a9f80-1162">Configure allowed transports</span></span>

<span data-ttu-id="a9f80-1163">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a9f80-1164">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a9f80-1165">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="a9f80-1166">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a9f80-1167">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a9f80-1168">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="a9f80-1168">Configure bearer authentication</span></span>

<span data-ttu-id="a9f80-1169">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a9f80-1170">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a9f80-1171">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a9f80-1172">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a9f80-1173">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a9f80-1174">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a9f80-1175">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a9f80-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a9f80-1177">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a9f80-1178">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a9f80-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a9f80-1179">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-1181">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1181">Option</span></span> | <span data-ttu-id="a9f80-1182">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1182">Default value</span></span> | <span data-ttu-id="a9f80-1183">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a9f80-1184">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-1185">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1185">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-1186">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-1187">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-1188">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a9f80-1189">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-1189">15 seconds</span></span> | <span data-ttu-id="a9f80-1190">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-1191">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a9f80-1192">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-1193">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a9f80-1194">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a9f80-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-1196">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1196">Option</span></span> | <span data-ttu-id="a9f80-1197">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1197">Default value</span></span> | <span data-ttu-id="a9f80-1198">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a9f80-1199">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-1200">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1200">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-1201">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a9f80-1202">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-1203">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-1204">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-1205">Opção</span><span class="sxs-lookup"><span data-stu-id="a9f80-1205">Option</span></span> | <span data-ttu-id="a9f80-1206">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1206">Default value</span></span> | <span data-ttu-id="a9f80-1207">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a9f80-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a9f80-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a9f80-1209">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="a9f80-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a9f80-1210">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1210">Timeout for server activity.</span></span> <span data-ttu-id="a9f80-1211">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-1212">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a9f80-1213">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` , para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a9f80-1214">15 s</span><span class="sxs-lookup"><span data-stu-id="a9f80-1214">15 seconds</span></span> | <span data-ttu-id="a9f80-1215">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="a9f80-1216">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a9f80-1217">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a9f80-1218">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a9f80-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a9f80-1219">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-1219">Configure additional options</span></span>

<span data-ttu-id="a9f80-1220">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="a9f80-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a9f80-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a9f80-1222">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="a9f80-1222">.NET Option</span></span> |  <span data-ttu-id="a9f80-1223">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1223">Default value</span></span> | <span data-ttu-id="a9f80-1224">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a9f80-1225">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a9f80-1226">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-1227">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-1228">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a9f80-1229">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1229">Empty</span></span> | <span data-ttu-id="a9f80-1230">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a9f80-1231">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1231">Empty</span></span> | <span data-ttu-id="a9f80-1232">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a9f80-1233">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1233">Empty</span></span> | <span data-ttu-id="a9f80-1234">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a9f80-1235">5 segundos</span><span class="sxs-lookup"><span data-stu-id="a9f80-1235">5 seconds</span></span> | <span data-ttu-id="a9f80-1236">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1236">WebSockets only.</span></span> <span data-ttu-id="a9f80-1237">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a9f80-1238">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a9f80-1239">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1239">Empty</span></span> | <span data-ttu-id="a9f80-1240">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a9f80-1241">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a9f80-1242">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="a9f80-1243">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a9f80-1244">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a9f80-1245">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="a9f80-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a9f80-1246">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a9f80-1247">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a9f80-1248">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a9f80-1249">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a9f80-1250">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a9f80-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a9f80-1252">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="a9f80-1252">JavaScript Option</span></span> | <span data-ttu-id="a9f80-1253">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1253">Default Value</span></span> | <span data-ttu-id="a9f80-1254">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a9f80-1255">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a9f80-1256">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a9f80-1257">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a9f80-1258">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-1259">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-1260">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a9f80-1261">Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="a9f80-1262">Opção Java</span><span class="sxs-lookup"><span data-stu-id="a9f80-1262">Java Option</span></span> | <span data-ttu-id="a9f80-1263">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="a9f80-1263">Default Value</span></span> | <span data-ttu-id="a9f80-1264">Descrição</span><span class="sxs-lookup"><span data-stu-id="a9f80-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a9f80-1265">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a9f80-1266">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a9f80-1267">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a9f80-1268">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="a9f80-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a9f80-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a9f80-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a9f80-1270">Vazio</span><span class="sxs-lookup"><span data-stu-id="a9f80-1270">Empty</span></span> | <span data-ttu-id="a9f80-1271">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9f80-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a9f80-1272">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a9f80-1273">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a9f80-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a9f80-1274">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a9f80-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a9f80-1275">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9f80-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
