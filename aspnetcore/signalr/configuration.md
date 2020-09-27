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
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393867"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="d0570-103">Configuração de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d0570-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d0570-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d0570-105">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d0570-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d0570-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d0570-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d0570-108">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0570-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d0570-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d0570-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d0570-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d0570-111">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d0570-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d0570-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0570-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d0570-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d0570-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d0570-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d0570-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d0570-116">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="d0570-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d0570-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d0570-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d0570-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-118">MessagePack serialization options</span></span>

<span data-ttu-id="d0570-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d0570-120">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d0570-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d0570-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d0570-122">Configure server options</span></span>

<span data-ttu-id="d0570-123">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d0570-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d0570-124">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-124">Option</span></span> | <span data-ttu-id="d0570-125">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-125">Default Value</span></span> | <span data-ttu-id="d0570-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d0570-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-127">30 seconds</span></span> | <span data-ttu-id="d0570-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d0570-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d0570-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d0570-130">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d0570-131">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-131">15 seconds</span></span> | <span data-ttu-id="d0570-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d0570-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d0570-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-134">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-135">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-135">15 seconds</span></span> | <span data-ttu-id="d0570-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d0570-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d0570-137">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d0570-138">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d0570-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d0570-139">All installed protocols</span></span> | <span data-ttu-id="d0570-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d0570-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d0570-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d0570-142">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d0570-143">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d0570-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d0570-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d0570-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d0570-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d0570-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-146">32 KB</span></span> | <span data-ttu-id="d0570-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="d0570-148">1</span><span class="sxs-lookup"><span data-stu-id="d0570-148">1</span></span> | <span data-ttu-id="d0570-149">O número máximo de métodos de Hub que cada cliente pode chamar em paralelo antes de enfileirar.</span><span class="sxs-lookup"><span data-stu-id="d0570-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="d0570-150">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d0570-151">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d0570-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d0570-152">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d0570-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d0570-153">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d0570-154">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d0570-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d0570-155">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0570-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d0570-156">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-156">Option</span></span> | <span data-ttu-id="d0570-157">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-157">Default Value</span></span> | <span data-ttu-id="d0570-158">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d0570-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-159">32 KB</span></span> | <span data-ttu-id="d0570-160">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d0570-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d0570-161">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d0570-162">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d0570-163">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d0570-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-164">32 KB</span></span> | <span data-ttu-id="d0570-165">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d0570-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d0570-166">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d0570-167">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d0570-167">All Transports are enabled.</span></span> | <span data-ttu-id="d0570-168">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d0570-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d0570-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-169">See below.</span></span> | <span data-ttu-id="d0570-170">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d0570-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d0570-171">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-171">See below.</span></span> | <span data-ttu-id="d0570-172">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d0570-173">0</span><span class="sxs-lookup"><span data-stu-id="d0570-173">0</span></span> | <span data-ttu-id="d0570-174">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d0570-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d0570-175">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d0570-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d0570-176">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d0570-177">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-177">Option</span></span> | <span data-ttu-id="d0570-178">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-178">Default Value</span></span> | <span data-ttu-id="d0570-179">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d0570-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-180">90 seconds</span></span> | <span data-ttu-id="d0570-181">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d0570-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d0570-182">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d0570-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d0570-183">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d0570-184">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-184">Option</span></span> | <span data-ttu-id="d0570-185">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-185">Default Value</span></span> | <span data-ttu-id="d0570-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d0570-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-187">5 seconds</span></span> | <span data-ttu-id="d0570-188">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d0570-189">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d0570-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d0570-190">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d0570-191">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d0570-191">Configure client options</span></span>

<span data-ttu-id="d0570-192">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d0570-193">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d0570-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d0570-194">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d0570-194">Configure logging</span></span>

<span data-ttu-id="d0570-195">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d0570-196">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d0570-197">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d0570-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-198">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d0570-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d0570-199">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d0570-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d0570-200">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d0570-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d0570-201">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d0570-202">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d0570-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d0570-203">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d0570-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d0570-204">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d0570-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d0570-205">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d0570-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d0570-206">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="d0570-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d0570-207">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d0570-207">The following table lists the available log levels.</span></span> <span data-ttu-id="d0570-208">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d0570-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d0570-209">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d0570-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d0570-210">String</span><span class="sxs-lookup"><span data-stu-id="d0570-210">String</span></span>                      | <span data-ttu-id="d0570-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d0570-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d0570-212">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d0570-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d0570-213">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d0570-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d0570-214">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d0570-215">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d0570-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d0570-216">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d0570-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d0570-217">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d0570-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d0570-218">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d0570-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d0570-219">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d0570-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d0570-220">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d0570-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d0570-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d0570-221">Configure allowed transports</span></span>

<span data-ttu-id="d0570-222">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d0570-223">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d0570-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d0570-224">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d0570-224">All transports are enabled by default.</span></span>

<span data-ttu-id="d0570-225">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d0570-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d0570-226">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d0570-227">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d0570-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d0570-228">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d0570-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d0570-229">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d0570-230">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d0570-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d0570-231">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d0570-231">Configure bearer authentication</span></span>

<span data-ttu-id="d0570-232">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d0570-233">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d0570-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d0570-234">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d0570-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d0570-235">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d0570-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d0570-236">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d0570-237">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d0570-238">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d0570-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d0570-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d0570-240">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d0570-241">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d0570-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d0570-242">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d0570-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-243">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-244">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-244">Option</span></span> | <span data-ttu-id="d0570-245">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-245">Default value</span></span> | <span data-ttu-id="d0570-246">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d0570-247">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-248">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-248">Timeout for server activity.</span></span> <span data-ttu-id="d0570-249">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-250">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-251">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d0570-252">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-252">15 seconds</span></span> | <span data-ttu-id="d0570-253">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-254">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-255">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-256">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-257">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-257">15 seconds</span></span> | <span data-ttu-id="d0570-258">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-259">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-260">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d0570-261">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d0570-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d0570-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-263">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-263">Option</span></span> | <span data-ttu-id="d0570-264">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-264">Default value</span></span> | <span data-ttu-id="d0570-265">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d0570-266">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-267">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-267">Timeout for server activity.</span></span> <span data-ttu-id="d0570-268">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d0570-269">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-270">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d0570-271">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-272">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-273">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-274">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-275">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-275">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-276">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-276">Option</span></span> | <span data-ttu-id="d0570-277">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-277">Default value</span></span> | <span data-ttu-id="d0570-278">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d0570-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d0570-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d0570-280">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-281">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-281">Timeout for server activity.</span></span> <span data-ttu-id="d0570-282">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-283">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-284">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d0570-285">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-285">15 seconds</span></span> | <span data-ttu-id="d0570-286">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-287">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-288">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-289">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d0570-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d0570-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d0570-291">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-292">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-293">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-294">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d0570-295">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-295">Configure additional options</span></span>

<span data-ttu-id="d0570-296">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d0570-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-297">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-298">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d0570-298">.NET Option</span></span> |  <span data-ttu-id="d0570-299">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-299">Default value</span></span> | <span data-ttu-id="d0570-300">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d0570-301">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d0570-302">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-303">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-304">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d0570-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-305">Empty</span></span> | <span data-ttu-id="d0570-306">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d0570-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d0570-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-307">Empty</span></span> | <span data-ttu-id="d0570-308">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d0570-309">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-309">Empty</span></span> | <span data-ttu-id="d0570-310">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d0570-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-311">5 seconds</span></span> | <span data-ttu-id="d0570-312">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-312">WebSockets only.</span></span> <span data-ttu-id="d0570-313">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d0570-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d0570-314">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d0570-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d0570-315">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-315">Empty</span></span> | <span data-ttu-id="d0570-316">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d0570-317">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d0570-318">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="d0570-319">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d0570-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d0570-320">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d0570-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d0570-321">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d0570-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d0570-322">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d0570-323">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d0570-324">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d0570-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d0570-325">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d0570-326">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d0570-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d0570-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-328">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-328">JavaScript Option</span></span> | <span data-ttu-id="d0570-329">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-329">Default Value</span></span> | <span data-ttu-id="d0570-330">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d0570-331">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="d0570-332">Dicionário de cabeçalhos enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="d0570-333">O envio de cabeçalhos no navegador não funciona para WebSockets ou <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> fluxo.</span><span class="sxs-lookup"><span data-stu-id="d0570-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d0570-334">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d0570-335">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-336">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-337">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="d0570-338">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="d0570-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="d0570-339">Azure App serviço usa cookie s para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="d0570-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="d0570-340">Para obter mais informações sobre o CORS com SignalR , consulte <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="d0570-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-341">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-341">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-342">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d0570-342">Java Option</span></span> | <span data-ttu-id="d0570-343">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-343">Default Value</span></span> | <span data-ttu-id="d0570-344">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d0570-345">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d0570-346">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-347">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-348">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d0570-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d0570-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d0570-350">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-350">Empty</span></span> | <span data-ttu-id="d0570-351">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d0570-352">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d0570-353">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d0570-354">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d0570-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d0570-355">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d0570-356">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d0570-357">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d0570-358">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d0570-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d0570-359">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d0570-360">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0570-361">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d0570-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d0570-362">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d0570-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d0570-363">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d0570-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d0570-364">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0570-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d0570-365">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d0570-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d0570-366">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d0570-367">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d0570-368">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="d0570-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d0570-369">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d0570-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d0570-370">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-370">MessagePack serialization options</span></span>

<span data-ttu-id="d0570-371">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d0570-372">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d0570-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-373">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d0570-374">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d0570-374">Configure server options</span></span>

<span data-ttu-id="d0570-375">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d0570-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d0570-376">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-376">Option</span></span> | <span data-ttu-id="d0570-377">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-377">Default Value</span></span> | <span data-ttu-id="d0570-378">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d0570-379">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-379">30 seconds</span></span> | <span data-ttu-id="d0570-380">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d0570-381">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d0570-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d0570-382">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d0570-383">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-383">15 seconds</span></span> | <span data-ttu-id="d0570-384">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d0570-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d0570-385">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-386">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-387">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-387">15 seconds</span></span> | <span data-ttu-id="d0570-388">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d0570-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d0570-389">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d0570-390">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d0570-391">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d0570-391">All installed protocols</span></span> | <span data-ttu-id="d0570-392">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-392">Protocols supported by this hub.</span></span> <span data-ttu-id="d0570-393">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d0570-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d0570-394">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d0570-395">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d0570-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d0570-396">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d0570-397">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d0570-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d0570-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-398">32 KB</span></span> | <span data-ttu-id="d0570-399">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d0570-400">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d0570-401">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d0570-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d0570-402">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d0570-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d0570-403">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d0570-404">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d0570-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d0570-405">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0570-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d0570-406">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-406">Option</span></span> | <span data-ttu-id="d0570-407">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-407">Default Value</span></span> | <span data-ttu-id="d0570-408">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d0570-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-409">32 KB</span></span> | <span data-ttu-id="d0570-410">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d0570-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d0570-411">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d0570-412">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d0570-413">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d0570-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-414">32 KB</span></span> | <span data-ttu-id="d0570-415">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d0570-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d0570-416">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d0570-417">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d0570-417">All Transports are enabled.</span></span> | <span data-ttu-id="d0570-418">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d0570-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d0570-419">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-419">See below.</span></span> | <span data-ttu-id="d0570-420">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d0570-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d0570-421">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-421">See below.</span></span> | <span data-ttu-id="d0570-422">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d0570-423">0</span><span class="sxs-lookup"><span data-stu-id="d0570-423">0</span></span> | <span data-ttu-id="d0570-424">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d0570-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d0570-425">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d0570-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d0570-426">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d0570-427">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-427">Option</span></span> | <span data-ttu-id="d0570-428">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-428">Default Value</span></span> | <span data-ttu-id="d0570-429">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d0570-430">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-430">90 seconds</span></span> | <span data-ttu-id="d0570-431">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d0570-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d0570-432">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d0570-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d0570-433">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d0570-434">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-434">Option</span></span> | <span data-ttu-id="d0570-435">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-435">Default Value</span></span> | <span data-ttu-id="d0570-436">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d0570-437">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-437">5 seconds</span></span> | <span data-ttu-id="d0570-438">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d0570-439">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d0570-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d0570-440">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d0570-441">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d0570-441">Configure client options</span></span>

<span data-ttu-id="d0570-442">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d0570-443">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d0570-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d0570-444">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d0570-444">Configure logging</span></span>

<span data-ttu-id="d0570-445">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d0570-446">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d0570-447">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d0570-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-448">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d0570-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d0570-449">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d0570-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d0570-450">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d0570-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d0570-451">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d0570-452">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d0570-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d0570-453">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d0570-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d0570-454">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d0570-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d0570-455">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d0570-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d0570-456">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="d0570-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d0570-457">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d0570-457">The following table lists the available log levels.</span></span> <span data-ttu-id="d0570-458">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d0570-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d0570-459">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d0570-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d0570-460">String</span><span class="sxs-lookup"><span data-stu-id="d0570-460">String</span></span>                      | <span data-ttu-id="d0570-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d0570-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d0570-462">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d0570-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d0570-463">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d0570-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d0570-464">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d0570-465">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d0570-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d0570-466">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d0570-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d0570-467">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d0570-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d0570-468">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d0570-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d0570-469">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d0570-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d0570-470">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d0570-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d0570-471">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d0570-471">Configure allowed transports</span></span>

<span data-ttu-id="d0570-472">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d0570-473">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d0570-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d0570-474">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d0570-474">All transports are enabled by default.</span></span>

<span data-ttu-id="d0570-475">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d0570-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d0570-476">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d0570-477">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d0570-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d0570-478">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d0570-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d0570-479">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d0570-480">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d0570-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d0570-481">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d0570-481">Configure bearer authentication</span></span>

<span data-ttu-id="d0570-482">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d0570-483">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d0570-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d0570-484">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d0570-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d0570-485">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d0570-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d0570-486">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d0570-487">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d0570-488">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d0570-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d0570-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d0570-490">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d0570-491">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d0570-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d0570-492">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d0570-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-493">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-494">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-494">Option</span></span> | <span data-ttu-id="d0570-495">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-495">Default value</span></span> | <span data-ttu-id="d0570-496">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d0570-497">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-498">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-498">Timeout for server activity.</span></span> <span data-ttu-id="d0570-499">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-500">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-501">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d0570-502">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-502">15 seconds</span></span> | <span data-ttu-id="d0570-503">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-504">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-505">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-506">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-507">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-507">15 seconds</span></span> | <span data-ttu-id="d0570-508">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-509">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-510">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d0570-511">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d0570-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d0570-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-513">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-513">Option</span></span> | <span data-ttu-id="d0570-514">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-514">Default value</span></span> | <span data-ttu-id="d0570-515">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d0570-516">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-517">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-517">Timeout for server activity.</span></span> <span data-ttu-id="d0570-518">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d0570-519">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-520">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d0570-521">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-522">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-523">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-524">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-525">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-525">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-526">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-526">Option</span></span> | <span data-ttu-id="d0570-527">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-527">Default value</span></span> | <span data-ttu-id="d0570-528">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d0570-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d0570-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d0570-530">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-531">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-531">Timeout for server activity.</span></span> <span data-ttu-id="d0570-532">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-533">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-534">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d0570-535">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-535">15 seconds</span></span> | <span data-ttu-id="d0570-536">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-537">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-538">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-539">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d0570-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d0570-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d0570-541">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-542">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-543">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-544">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d0570-545">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-545">Configure additional options</span></span>

<span data-ttu-id="d0570-546">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d0570-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-547">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-548">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d0570-548">.NET Option</span></span> |  <span data-ttu-id="d0570-549">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-549">Default value</span></span> | <span data-ttu-id="d0570-550">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d0570-551">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d0570-552">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-553">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-554">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d0570-555">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-555">Empty</span></span> | <span data-ttu-id="d0570-556">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d0570-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d0570-557">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-557">Empty</span></span> | <span data-ttu-id="d0570-558">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d0570-559">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-559">Empty</span></span> | <span data-ttu-id="d0570-560">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d0570-561">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-561">5 seconds</span></span> | <span data-ttu-id="d0570-562">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-562">WebSockets only.</span></span> <span data-ttu-id="d0570-563">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d0570-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d0570-564">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d0570-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d0570-565">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-565">Empty</span></span> | <span data-ttu-id="d0570-566">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d0570-567">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d0570-568">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="d0570-569">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d0570-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d0570-570">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d0570-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d0570-571">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d0570-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d0570-572">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d0570-573">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d0570-574">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d0570-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d0570-575">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d0570-576">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d0570-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d0570-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-578">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-578">JavaScript Option</span></span> | <span data-ttu-id="d0570-579">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-579">Default Value</span></span> | <span data-ttu-id="d0570-580">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d0570-581">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d0570-582">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d0570-583">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-584">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-585">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-586">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-586">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-587">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d0570-587">Java Option</span></span> | <span data-ttu-id="d0570-588">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-588">Default Value</span></span> | <span data-ttu-id="d0570-589">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d0570-590">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d0570-591">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-592">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-593">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d0570-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d0570-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d0570-595">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-595">Empty</span></span> | <span data-ttu-id="d0570-596">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d0570-597">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d0570-598">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d0570-599">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d0570-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d0570-600">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d0570-601">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d0570-602">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d0570-603">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d0570-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d0570-604">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d0570-605">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0570-606">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d0570-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d0570-607">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d0570-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d0570-608">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d0570-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d0570-609">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0570-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="d0570-610">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d0570-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d0570-611">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d0570-612">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d0570-613">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="d0570-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d0570-614">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d0570-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d0570-615">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-615">MessagePack serialization options</span></span>

<span data-ttu-id="d0570-616">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d0570-617">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d0570-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-618">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d0570-619">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d0570-619">Configure server options</span></span>

<span data-ttu-id="d0570-620">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d0570-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d0570-621">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-621">Option</span></span> | <span data-ttu-id="d0570-622">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-622">Default Value</span></span> | <span data-ttu-id="d0570-623">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d0570-624">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-624">30 seconds</span></span> | <span data-ttu-id="d0570-625">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d0570-626">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d0570-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d0570-627">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d0570-628">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-628">15 seconds</span></span> | <span data-ttu-id="d0570-629">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d0570-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d0570-630">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-631">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-632">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-632">15 seconds</span></span> | <span data-ttu-id="d0570-633">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d0570-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d0570-634">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d0570-635">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d0570-636">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d0570-636">All installed protocols</span></span> | <span data-ttu-id="d0570-637">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-637">Protocols supported by this hub.</span></span> <span data-ttu-id="d0570-638">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d0570-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d0570-639">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d0570-640">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d0570-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d0570-641">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d0570-642">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d0570-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d0570-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-643">32 KB</span></span> | <span data-ttu-id="d0570-644">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d0570-645">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d0570-646">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d0570-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d0570-647">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d0570-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d0570-648">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d0570-649">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d0570-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d0570-650">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0570-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d0570-651">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-651">Option</span></span> | <span data-ttu-id="d0570-652">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-652">Default Value</span></span> | <span data-ttu-id="d0570-653">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d0570-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-654">32 KB</span></span> | <span data-ttu-id="d0570-655">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d0570-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d0570-656">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d0570-657">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d0570-658">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d0570-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-659">32 KB</span></span> | <span data-ttu-id="d0570-660">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d0570-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d0570-661">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d0570-662">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d0570-662">All Transports are enabled.</span></span> | <span data-ttu-id="d0570-663">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d0570-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d0570-664">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-664">See below.</span></span> | <span data-ttu-id="d0570-665">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d0570-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d0570-666">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-666">See below.</span></span> | <span data-ttu-id="d0570-667">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d0570-668">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d0570-669">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-669">Option</span></span> | <span data-ttu-id="d0570-670">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-670">Default Value</span></span> | <span data-ttu-id="d0570-671">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d0570-672">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-672">90 seconds</span></span> | <span data-ttu-id="d0570-673">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d0570-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d0570-674">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d0570-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d0570-675">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d0570-676">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-676">Option</span></span> | <span data-ttu-id="d0570-677">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-677">Default Value</span></span> | <span data-ttu-id="d0570-678">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d0570-679">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-679">5 seconds</span></span> | <span data-ttu-id="d0570-680">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d0570-681">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d0570-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d0570-682">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d0570-683">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d0570-683">Configure client options</span></span>

<span data-ttu-id="d0570-684">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d0570-685">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d0570-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d0570-686">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d0570-686">Configure logging</span></span>

<span data-ttu-id="d0570-687">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d0570-688">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d0570-689">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d0570-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-690">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d0570-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d0570-691">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d0570-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d0570-692">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d0570-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d0570-693">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d0570-694">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d0570-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d0570-695">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d0570-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d0570-696">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d0570-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d0570-697">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d0570-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d0570-698">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="d0570-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d0570-699">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d0570-699">The following table lists the available log levels.</span></span> <span data-ttu-id="d0570-700">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d0570-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d0570-701">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d0570-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d0570-702">String</span><span class="sxs-lookup"><span data-stu-id="d0570-702">String</span></span>                      | <span data-ttu-id="d0570-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d0570-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d0570-704">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d0570-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d0570-705">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d0570-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d0570-706">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d0570-707">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d0570-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d0570-708">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d0570-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d0570-709">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d0570-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d0570-710">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d0570-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d0570-711">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d0570-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d0570-712">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d0570-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d0570-713">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d0570-713">Configure allowed transports</span></span>

<span data-ttu-id="d0570-714">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d0570-715">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d0570-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d0570-716">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d0570-716">All transports are enabled by default.</span></span>

<span data-ttu-id="d0570-717">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d0570-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d0570-718">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d0570-719">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d0570-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d0570-720">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d0570-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d0570-721">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d0570-722">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d0570-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d0570-723">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d0570-723">Configure bearer authentication</span></span>

<span data-ttu-id="d0570-724">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d0570-725">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d0570-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d0570-726">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d0570-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d0570-727">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d0570-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d0570-728">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d0570-729">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d0570-730">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d0570-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d0570-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d0570-732">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d0570-733">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d0570-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d0570-734">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d0570-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-735">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-736">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-736">Option</span></span> | <span data-ttu-id="d0570-737">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-737">Default value</span></span> | <span data-ttu-id="d0570-738">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d0570-739">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-740">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-740">Timeout for server activity.</span></span> <span data-ttu-id="d0570-741">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-742">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-743">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d0570-744">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-744">15 seconds</span></span> | <span data-ttu-id="d0570-745">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-746">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-747">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-748">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-749">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-749">15 seconds</span></span> | <span data-ttu-id="d0570-750">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-751">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-752">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d0570-753">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d0570-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d0570-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-755">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-755">Option</span></span> | <span data-ttu-id="d0570-756">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-756">Default value</span></span> | <span data-ttu-id="d0570-757">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d0570-758">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-759">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-759">Timeout for server activity.</span></span> <span data-ttu-id="d0570-760">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d0570-761">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-762">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d0570-763">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-764">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-765">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-766">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-767">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-767">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-768">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-768">Option</span></span> | <span data-ttu-id="d0570-769">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-769">Default value</span></span> | <span data-ttu-id="d0570-770">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d0570-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d0570-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d0570-772">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-773">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-773">Timeout for server activity.</span></span> <span data-ttu-id="d0570-774">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-775">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-776">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d0570-777">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-777">15 seconds</span></span> | <span data-ttu-id="d0570-778">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-779">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-780">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-781">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d0570-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d0570-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d0570-783">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-784">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-785">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-786">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d0570-787">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-787">Configure additional options</span></span>

<span data-ttu-id="d0570-788">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d0570-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-789">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-790">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d0570-790">.NET Option</span></span> |  <span data-ttu-id="d0570-791">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-791">Default value</span></span> | <span data-ttu-id="d0570-792">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d0570-793">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d0570-794">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-795">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-796">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d0570-797">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-797">Empty</span></span> | <span data-ttu-id="d0570-798">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d0570-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d0570-799">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-799">Empty</span></span> | <span data-ttu-id="d0570-800">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d0570-801">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-801">Empty</span></span> | <span data-ttu-id="d0570-802">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d0570-803">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-803">5 seconds</span></span> | <span data-ttu-id="d0570-804">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-804">WebSockets only.</span></span> <span data-ttu-id="d0570-805">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d0570-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d0570-806">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d0570-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d0570-807">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-807">Empty</span></span> | <span data-ttu-id="d0570-808">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d0570-809">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d0570-810">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="d0570-811">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d0570-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d0570-812">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d0570-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d0570-813">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d0570-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d0570-814">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d0570-815">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d0570-816">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d0570-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d0570-817">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d0570-818">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d0570-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d0570-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-820">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-820">JavaScript Option</span></span> | <span data-ttu-id="d0570-821">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-821">Default Value</span></span> | <span data-ttu-id="d0570-822">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d0570-823">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d0570-824">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d0570-825">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-826">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-827">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-828">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-828">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-829">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d0570-829">Java Option</span></span> | <span data-ttu-id="d0570-830">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-830">Default Value</span></span> | <span data-ttu-id="d0570-831">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d0570-832">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d0570-833">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-834">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-835">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d0570-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d0570-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d0570-837">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-837">Empty</span></span> | <span data-ttu-id="d0570-838">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d0570-839">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d0570-840">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d0570-841">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d0570-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d0570-842">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d0570-843">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d0570-844">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d0570-845">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d0570-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d0570-846">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d0570-847">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d0570-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d0570-848">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d0570-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d0570-849">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d0570-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d0570-850">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0570-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d0570-851">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d0570-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d0570-852">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d0570-853">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d0570-854">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-854">MessagePack serialization options</span></span>

<span data-ttu-id="d0570-855">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d0570-856">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d0570-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-857">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d0570-858">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d0570-858">Configure server options</span></span>

<span data-ttu-id="d0570-859">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d0570-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d0570-860">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-860">Option</span></span> | <span data-ttu-id="d0570-861">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-861">Default Value</span></span> | <span data-ttu-id="d0570-862">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d0570-863">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-863">30 seconds</span></span> | <span data-ttu-id="d0570-864">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d0570-865">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d0570-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d0570-866">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d0570-867">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-867">15 seconds</span></span> | <span data-ttu-id="d0570-868">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d0570-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d0570-869">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-870">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-871">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-871">15 seconds</span></span> | <span data-ttu-id="d0570-872">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d0570-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d0570-873">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d0570-874">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d0570-875">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d0570-875">All installed protocols</span></span> | <span data-ttu-id="d0570-876">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-876">Protocols supported by this hub.</span></span> <span data-ttu-id="d0570-877">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d0570-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d0570-878">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d0570-879">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d0570-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d0570-880">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d0570-881">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d0570-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d0570-882">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d0570-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d0570-883">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d0570-884">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d0570-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d0570-885">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0570-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d0570-886">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-886">Option</span></span> | <span data-ttu-id="d0570-887">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-887">Default Value</span></span> | <span data-ttu-id="d0570-888">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d0570-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-889">32 KB</span></span> | <span data-ttu-id="d0570-890">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d0570-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d0570-891">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d0570-892">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d0570-893">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d0570-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-894">32 KB</span></span> | <span data-ttu-id="d0570-895">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d0570-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d0570-896">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d0570-897">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d0570-897">All Transports are enabled.</span></span> | <span data-ttu-id="d0570-898">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d0570-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d0570-899">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-899">See below.</span></span> | <span data-ttu-id="d0570-900">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d0570-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d0570-901">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-901">See below.</span></span> | <span data-ttu-id="d0570-902">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d0570-903">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d0570-904">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-904">Option</span></span> | <span data-ttu-id="d0570-905">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-905">Default Value</span></span> | <span data-ttu-id="d0570-906">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d0570-907">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-907">90 seconds</span></span> | <span data-ttu-id="d0570-908">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d0570-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d0570-909">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d0570-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d0570-910">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d0570-911">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-911">Option</span></span> | <span data-ttu-id="d0570-912">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-912">Default Value</span></span> | <span data-ttu-id="d0570-913">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d0570-914">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-914">5 seconds</span></span> | <span data-ttu-id="d0570-915">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d0570-916">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d0570-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d0570-917">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d0570-918">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d0570-918">Configure client options</span></span>

<span data-ttu-id="d0570-919">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d0570-920">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d0570-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d0570-921">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d0570-921">Configure logging</span></span>

<span data-ttu-id="d0570-922">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d0570-923">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d0570-924">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d0570-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-925">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d0570-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d0570-926">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d0570-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d0570-927">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d0570-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d0570-928">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d0570-929">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d0570-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d0570-930">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d0570-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d0570-931">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d0570-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d0570-932">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d0570-933">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d0570-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d0570-934">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d0570-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d0570-935">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d0570-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d0570-936">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d0570-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d0570-937">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d0570-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d0570-938">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d0570-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d0570-939">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d0570-939">Configure allowed transports</span></span>

<span data-ttu-id="d0570-940">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d0570-941">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d0570-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d0570-942">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d0570-942">All transports are enabled by default.</span></span>

<span data-ttu-id="d0570-943">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d0570-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d0570-944">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d0570-945">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d0570-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d0570-946">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d0570-946">Configure bearer authentication</span></span>

<span data-ttu-id="d0570-947">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d0570-948">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d0570-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d0570-949">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d0570-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d0570-950">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d0570-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d0570-951">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d0570-952">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d0570-953">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d0570-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d0570-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d0570-955">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d0570-956">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d0570-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d0570-957">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d0570-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-958">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-959">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-959">Option</span></span> | <span data-ttu-id="d0570-960">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-960">Default value</span></span> | <span data-ttu-id="d0570-961">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d0570-962">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-963">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-963">Timeout for server activity.</span></span> <span data-ttu-id="d0570-964">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-965">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-966">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d0570-967">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-967">15 seconds</span></span> | <span data-ttu-id="d0570-968">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-969">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-970">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-971">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-972">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-972">15 seconds</span></span> | <span data-ttu-id="d0570-973">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-974">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-975">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d0570-976">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d0570-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d0570-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-978">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-978">Option</span></span> | <span data-ttu-id="d0570-979">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-979">Default value</span></span> | <span data-ttu-id="d0570-980">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d0570-981">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-982">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-982">Timeout for server activity.</span></span> <span data-ttu-id="d0570-983">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d0570-984">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-985">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d0570-986">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-987">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-988">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-989">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-990">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-990">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-991">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-991">Option</span></span> | <span data-ttu-id="d0570-992">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-992">Default value</span></span> | <span data-ttu-id="d0570-993">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d0570-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d0570-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d0570-995">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-996">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-996">Timeout for server activity.</span></span> <span data-ttu-id="d0570-997">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-998">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-999">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d0570-1000">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-1000">15 seconds</span></span> | <span data-ttu-id="d0570-1001">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-1002">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-1003">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-1004">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d0570-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d0570-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d0570-1006">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d0570-1007">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d0570-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d0570-1008">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d0570-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d0570-1009">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d0570-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d0570-1010">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-1010">Configure additional options</span></span>

<span data-ttu-id="d0570-1011">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d0570-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-1013">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d0570-1013">.NET Option</span></span> |  <span data-ttu-id="d0570-1014">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1014">Default value</span></span> | <span data-ttu-id="d0570-1015">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d0570-1016">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d0570-1017">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-1018">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-1019">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d0570-1020">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1020">Empty</span></span> | <span data-ttu-id="d0570-1021">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d0570-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d0570-1022">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1022">Empty</span></span> | <span data-ttu-id="d0570-1023">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d0570-1024">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1024">Empty</span></span> | <span data-ttu-id="d0570-1025">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d0570-1026">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-1026">5 seconds</span></span> | <span data-ttu-id="d0570-1027">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-1027">WebSockets only.</span></span> <span data-ttu-id="d0570-1028">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d0570-1029">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d0570-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d0570-1030">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1030">Empty</span></span> | <span data-ttu-id="d0570-1031">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d0570-1032">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d0570-1033">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="d0570-1034">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d0570-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d0570-1035">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d0570-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d0570-1036">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d0570-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d0570-1037">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d0570-1038">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d0570-1039">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d0570-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d0570-1040">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d0570-1041">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d0570-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d0570-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-1043">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-1043">JavaScript Option</span></span> | <span data-ttu-id="d0570-1044">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1044">Default Value</span></span> | <span data-ttu-id="d0570-1045">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d0570-1046">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d0570-1047">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d0570-1048">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-1049">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-1050">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-1051">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-1052">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d0570-1052">Java Option</span></span> | <span data-ttu-id="d0570-1053">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1053">Default Value</span></span> | <span data-ttu-id="d0570-1054">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d0570-1055">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d0570-1056">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-1057">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-1058">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d0570-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d0570-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d0570-1060">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1060">Empty</span></span> | <span data-ttu-id="d0570-1061">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d0570-1062">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d0570-1063">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d0570-1064">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d0570-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d0570-1065">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d0570-1066">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d0570-1067">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d0570-1068">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d0570-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d0570-1069">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d0570-1070">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d0570-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d0570-1071">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d0570-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d0570-1072">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d0570-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d0570-1073">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d0570-1074">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d0570-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d0570-1075">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d0570-1076">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d0570-1077">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d0570-1077">MessagePack serialization options</span></span>

<span data-ttu-id="d0570-1078">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d0570-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d0570-1079">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d0570-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-1080">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d0570-1081">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d0570-1081">Configure server options</span></span>

<span data-ttu-id="d0570-1082">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d0570-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d0570-1083">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1083">Option</span></span> | <span data-ttu-id="d0570-1084">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1084">Default Value</span></span> | <span data-ttu-id="d0570-1085">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d0570-1086">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-1086">15 seconds</span></span> | <span data-ttu-id="d0570-1087">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d0570-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d0570-1088">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-1089">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d0570-1090">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-1090">15 seconds</span></span> | <span data-ttu-id="d0570-1091">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d0570-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d0570-1092">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d0570-1093">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d0570-1094">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d0570-1094">All installed protocols</span></span> | <span data-ttu-id="d0570-1095">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="d0570-1096">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d0570-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d0570-1097">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d0570-1098">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d0570-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d0570-1099">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0570-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d0570-1100">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d0570-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d0570-1101">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d0570-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d0570-1102">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d0570-1103">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d0570-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d0570-1104">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0570-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d0570-1105">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1105">Option</span></span> | <span data-ttu-id="d0570-1106">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1106">Default Value</span></span> | <span data-ttu-id="d0570-1107">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d0570-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-1108">32 KB</span></span> | <span data-ttu-id="d0570-1109">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d0570-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d0570-1110">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d0570-1111">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d0570-1112">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d0570-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d0570-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="d0570-1113">32 KB</span></span> | <span data-ttu-id="d0570-1114">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d0570-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d0570-1115">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d0570-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d0570-1116">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d0570-1116">All Transports are enabled.</span></span> | <span data-ttu-id="d0570-1117">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d0570-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d0570-1118">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-1118">See below.</span></span> | <span data-ttu-id="d0570-1119">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d0570-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d0570-1120">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d0570-1120">See below.</span></span> | <span data-ttu-id="d0570-1121">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d0570-1122">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d0570-1123">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1123">Option</span></span> | <span data-ttu-id="d0570-1124">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1124">Default Value</span></span> | <span data-ttu-id="d0570-1125">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d0570-1126">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-1126">90 seconds</span></span> | <span data-ttu-id="d0570-1127">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d0570-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d0570-1128">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d0570-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d0570-1129">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d0570-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d0570-1130">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1130">Option</span></span> | <span data-ttu-id="d0570-1131">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1131">Default Value</span></span> | <span data-ttu-id="d0570-1132">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d0570-1133">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-1133">5 seconds</span></span> | <span data-ttu-id="d0570-1134">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d0570-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d0570-1135">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d0570-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d0570-1136">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d0570-1137">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d0570-1137">Configure client options</span></span>

<span data-ttu-id="d0570-1138">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d0570-1139">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d0570-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d0570-1140">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d0570-1140">Configure logging</span></span>

<span data-ttu-id="d0570-1141">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d0570-1142">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d0570-1143">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d0570-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d0570-1144">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d0570-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d0570-1145">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d0570-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d0570-1146">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d0570-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d0570-1147">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d0570-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d0570-1148">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d0570-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d0570-1149">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d0570-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d0570-1150">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d0570-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d0570-1151">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d0570-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d0570-1152">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d0570-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d0570-1153">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d0570-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d0570-1154">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d0570-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d0570-1155">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d0570-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d0570-1156">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d0570-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d0570-1157">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d0570-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d0570-1158">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d0570-1158">Configure allowed transports</span></span>

<span data-ttu-id="d0570-1159">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d0570-1160">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d0570-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d0570-1161">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d0570-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="d0570-1162">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d0570-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d0570-1163">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d0570-1164">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d0570-1164">Configure bearer authentication</span></span>

<span data-ttu-id="d0570-1165">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d0570-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d0570-1166">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d0570-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d0570-1167">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d0570-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d0570-1168">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d0570-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d0570-1169">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d0570-1170">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d0570-1171">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d0570-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d0570-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d0570-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d0570-1173">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d0570-1174">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d0570-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d0570-1175">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d0570-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-1177">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1177">Option</span></span> | <span data-ttu-id="d0570-1178">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1178">Default value</span></span> | <span data-ttu-id="d0570-1179">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d0570-1180">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-1181">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1181">Timeout for server activity.</span></span> <span data-ttu-id="d0570-1182">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-1183">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-1184">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d0570-1185">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-1185">15 seconds</span></span> | <span data-ttu-id="d0570-1186">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-1187">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d0570-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d0570-1188">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-1189">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d0570-1190">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d0570-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d0570-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-1192">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1192">Option</span></span> | <span data-ttu-id="d0570-1193">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1193">Default value</span></span> | <span data-ttu-id="d0570-1194">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d0570-1195">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-1196">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1196">Timeout for server activity.</span></span> <span data-ttu-id="d0570-1197">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d0570-1198">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-1199">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-1200">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-1201">Opção</span><span class="sxs-lookup"><span data-stu-id="d0570-1201">Option</span></span> | <span data-ttu-id="d0570-1202">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1202">Default value</span></span> | <span data-ttu-id="d0570-1203">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d0570-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d0570-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d0570-1205">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d0570-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d0570-1206">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1206">Timeout for server activity.</span></span> <span data-ttu-id="d0570-1207">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-1208">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d0570-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d0570-1209">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` , para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d0570-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d0570-1210">15 s</span><span class="sxs-lookup"><span data-stu-id="d0570-1210">15 seconds</span></span> | <span data-ttu-id="d0570-1211">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0570-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="d0570-1212">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d0570-1213">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d0570-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d0570-1214">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d0570-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d0570-1215">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-1215">Configure additional options</span></span>

<span data-ttu-id="d0570-1216">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d0570-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d0570-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="d0570-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d0570-1218">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d0570-1218">.NET Option</span></span> |  <span data-ttu-id="d0570-1219">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1219">Default value</span></span> | <span data-ttu-id="d0570-1220">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d0570-1221">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d0570-1222">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-1223">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-1224">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d0570-1225">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1225">Empty</span></span> | <span data-ttu-id="d0570-1226">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d0570-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d0570-1227">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1227">Empty</span></span> | <span data-ttu-id="d0570-1228">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d0570-1229">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1229">Empty</span></span> | <span data-ttu-id="d0570-1230">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d0570-1231">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d0570-1231">5 seconds</span></span> | <span data-ttu-id="d0570-1232">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-1232">WebSockets only.</span></span> <span data-ttu-id="d0570-1233">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d0570-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d0570-1234">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d0570-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d0570-1235">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1235">Empty</span></span> | <span data-ttu-id="d0570-1236">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d0570-1237">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d0570-1238">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="d0570-1239">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d0570-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d0570-1240">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d0570-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d0570-1241">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d0570-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d0570-1242">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d0570-1243">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0570-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d0570-1244">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d0570-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d0570-1245">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d0570-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d0570-1246">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d0570-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d0570-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d0570-1248">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0570-1248">JavaScript Option</span></span> | <span data-ttu-id="d0570-1249">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1249">Default Value</span></span> | <span data-ttu-id="d0570-1250">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d0570-1251">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d0570-1252">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d0570-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d0570-1253">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-1254">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-1255">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d0570-1256">Java</span><span class="sxs-lookup"><span data-stu-id="d0570-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="d0570-1257">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d0570-1257">Java Option</span></span> | <span data-ttu-id="d0570-1258">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d0570-1258">Default Value</span></span> | <span data-ttu-id="d0570-1259">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0570-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d0570-1260">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d0570-1261">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d0570-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d0570-1262">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d0570-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d0570-1263">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d0570-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d0570-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d0570-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d0570-1265">Vazio</span><span class="sxs-lookup"><span data-stu-id="d0570-1265">Empty</span></span> | <span data-ttu-id="d0570-1266">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0570-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d0570-1267">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d0570-1268">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d0570-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d0570-1269">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d0570-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d0570-1270">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0570-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
