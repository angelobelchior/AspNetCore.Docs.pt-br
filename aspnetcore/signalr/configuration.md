---
title: 'Configuração de ASP.NET Core SignalR'
author: bradygaster
description: 'Saiba como configurar os SignalR aplicativos ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061230"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="6f0b5-103">Configuração de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="6f0b5-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6f0b5-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-105">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6f0b5-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6f0b5-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6f0b5-108">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f0b5-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6f0b5-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6f0b5-111">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6f0b5-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6f0b5-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6f0b5-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6f0b5-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6f0b5-116">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="6f0b5-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6f0b5-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6f0b5-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-118">MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6f0b5-120">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6f0b5-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6f0b5-122">Configure server options</span></span>

<span data-ttu-id="6f0b5-123">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6f0b5-124">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-124">Option</span></span> | <span data-ttu-id="6f0b5-125">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-125">Default Value</span></span> | <span data-ttu-id="6f0b5-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6f0b5-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-127">30 seconds</span></span> | <span data-ttu-id="6f0b5-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6f0b5-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6f0b5-130">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-131">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-131">15 seconds</span></span> | <span data-ttu-id="6f0b5-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6f0b5-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-134">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-135">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-135">15 seconds</span></span> | <span data-ttu-id="6f0b5-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6f0b5-137">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6f0b5-138">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6f0b5-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6f0b5-139">All installed protocols</span></span> | <span data-ttu-id="6f0b5-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-140">Protocols supported by this hub.</span></span> <span data-ttu-id="6f0b5-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6f0b5-142">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6f0b5-143">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6f0b5-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6f0b5-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6f0b5-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-146">32 KB</span></span> | <span data-ttu-id="6f0b5-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="6f0b5-148">1</span><span class="sxs-lookup"><span data-stu-id="6f0b5-148">1</span></span> | <span data-ttu-id="6f0b5-149">O número máximo de métodos de Hub que cada cliente pode chamar em paralelo antes de enfileirar.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="6f0b5-150">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6f0b5-151">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6f0b5-152">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6f0b5-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6f0b5-153">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6f0b5-154">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6f0b5-155">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6f0b5-156">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-156">Option</span></span> | <span data-ttu-id="6f0b5-157">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-157">Default Value</span></span> | <span data-ttu-id="6f0b5-158">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6f0b5-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-159">32 KB</span></span> | <span data-ttu-id="6f0b5-160">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6f0b5-161">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6f0b5-162">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6f0b5-163">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6f0b5-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-164">32 KB</span></span> | <span data-ttu-id="6f0b5-165">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6f0b5-166">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6f0b5-167">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-167">All Transports are enabled.</span></span> | <span data-ttu-id="6f0b5-168">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6f0b5-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-169">See below.</span></span> | <span data-ttu-id="6f0b5-170">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6f0b5-171">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-171">See below.</span></span> | <span data-ttu-id="6f0b5-172">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6f0b5-173">0</span><span class="sxs-lookup"><span data-stu-id="6f0b5-173">0</span></span> | <span data-ttu-id="6f0b5-174">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6f0b5-175">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6f0b5-176">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6f0b5-177">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-177">Option</span></span> | <span data-ttu-id="6f0b5-178">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-178">Default Value</span></span> | <span data-ttu-id="6f0b5-179">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6f0b5-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-180">90 seconds</span></span> | <span data-ttu-id="6f0b5-181">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6f0b5-182">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6f0b5-183">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6f0b5-184">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-184">Option</span></span> | <span data-ttu-id="6f0b5-185">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-185">Default Value</span></span> | <span data-ttu-id="6f0b5-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6f0b5-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-187">5 seconds</span></span> | <span data-ttu-id="6f0b5-188">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6f0b5-189">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6f0b5-190">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6f0b5-191">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6f0b5-191">Configure client options</span></span>

<span data-ttu-id="6f0b5-192">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6f0b5-193">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6f0b5-194">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6f0b5-194">Configure logging</span></span>

<span data-ttu-id="6f0b5-195">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6f0b5-196">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6f0b5-197">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-198">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6f0b5-199">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6f0b5-200">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6f0b5-201">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6f0b5-202">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6f0b5-203">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6f0b5-204">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6f0b5-205">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6f0b5-206">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6f0b5-207">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-207">The following table lists the available log levels.</span></span> <span data-ttu-id="6f0b5-208">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6f0b5-209">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela** serão registrados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="6f0b5-210">String</span><span class="sxs-lookup"><span data-stu-id="6f0b5-210">String</span></span>                      | <span data-ttu-id="6f0b5-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6f0b5-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6f0b5-212">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6f0b5-213">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6f0b5-214">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6f0b5-215">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6f0b5-216">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6f0b5-217">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6f0b5-218">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6f0b5-219">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6f0b5-220">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6f0b5-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-221">Configure allowed transports</span></span>

<span data-ttu-id="6f0b5-222">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6f0b5-223">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6f0b5-224">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-224">All transports are enabled by default.</span></span>

<span data-ttu-id="6f0b5-225">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6f0b5-226">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6f0b5-227">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6f0b5-228">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6f0b5-229">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6f0b5-230">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6f0b5-231">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6f0b5-231">Configure bearer authentication</span></span>

<span data-ttu-id="6f0b5-232">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6f0b5-233">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6f0b5-234">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6f0b5-235">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6f0b5-236">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6f0b5-237">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6f0b5-238">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6f0b5-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6f0b5-240">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6f0b5-241">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6f0b5-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6f0b5-242">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-243">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-244">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-244">Option</span></span> | <span data-ttu-id="6f0b5-245">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-245">Default value</span></span> | <span data-ttu-id="6f0b5-246">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6f0b5-247">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-248">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-248">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-249">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-250">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-251">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-252">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-252">15 seconds</span></span> | <span data-ttu-id="6f0b5-253">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-254">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-255">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-256">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-257">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-257">15 seconds</span></span> | <span data-ttu-id="6f0b5-258">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-259">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-260">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6f0b5-261">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-263">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-263">Option</span></span> | <span data-ttu-id="6f0b5-264">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-264">Default value</span></span> | <span data-ttu-id="6f0b5-265">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6f0b5-266">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-267">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-267">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-268">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6f0b5-269">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-270">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6f0b5-271">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-272">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-273">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-274">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-275">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-275">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-276">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-276">Option</span></span> | <span data-ttu-id="6f0b5-277">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-277">Default value</span></span> | <span data-ttu-id="6f0b5-278">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6f0b5-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6f0b5-280">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-281">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-281">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-282">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-283">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-284">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6f0b5-285">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-285">15 seconds</span></span> | <span data-ttu-id="6f0b5-286">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-287">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-288">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-289">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6f0b5-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6f0b5-291">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-292">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-293">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-294">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6f0b5-295">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-295">Configure additional options</span></span>

<span data-ttu-id="6f0b5-296">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-297">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-298">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-298">.NET Option</span></span> |  <span data-ttu-id="6f0b5-299">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-299">Default value</span></span> | <span data-ttu-id="6f0b5-300">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-301">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6f0b5-302">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-303">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-304">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6f0b5-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-305">Empty</span></span> | <span data-ttu-id="6f0b5-306">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6f0b5-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-307">Empty</span></span> | <span data-ttu-id="6f0b5-308">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6f0b5-309">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-309">Empty</span></span> | <span data-ttu-id="6f0b5-310">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6f0b5-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-311">5 seconds</span></span> | <span data-ttu-id="6f0b5-312">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-312">WebSockets only.</span></span> <span data-ttu-id="6f0b5-313">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6f0b5-314">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6f0b5-315">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-315">Empty</span></span> | <span data-ttu-id="6f0b5-316">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6f0b5-317">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6f0b5-318">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="6f0b5-319">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6f0b5-320">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6f0b5-321">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6f0b5-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6f0b5-322">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6f0b5-323">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6f0b5-324">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6f0b5-325">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6f0b5-326">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-328">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-328">JavaScript Option</span></span> | <span data-ttu-id="6f0b5-329">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-329">Default Value</span></span> | <span data-ttu-id="6f0b5-330">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6f0b5-331">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="6f0b5-332">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="6f0b5-333">Dicionário de cabeçalhos enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="6f0b5-334">O envio de cabeçalhos no navegador não funciona para WebSockets ou <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> fluxo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6f0b5-335">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6f0b5-336">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-337">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-338">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="6f0b5-339">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="6f0b5-340">Azure App serviço usa cookie s para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="6f0b5-341">Para obter mais informações sobre o CORS com SignalR , consulte <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-342">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-342">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-343">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-343">Java Option</span></span> | <span data-ttu-id="6f0b5-344">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-344">Default Value</span></span> | <span data-ttu-id="6f0b5-345">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-346">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6f0b5-347">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-348">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-349">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6f0b5-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6f0b5-351">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-351">Empty</span></span> | <span data-ttu-id="6f0b5-352">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6f0b5-353">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

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

<span data-ttu-id="6f0b5-354">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="6f0b5-355">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6f0b5-356">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6f0b5-357">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-358">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6f0b5-359">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6f0b5-360">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6f0b5-361">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f0b5-362">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6f0b5-363">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6f0b5-364">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6f0b5-365">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6f0b5-366">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6f0b5-367">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6f0b5-368">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6f0b5-369">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="6f0b5-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6f0b5-370">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6f0b5-371">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-371">MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-372">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6f0b5-373">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-374">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6f0b5-375">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6f0b5-375">Configure server options</span></span>

<span data-ttu-id="6f0b5-376">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6f0b5-377">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-377">Option</span></span> | <span data-ttu-id="6f0b5-378">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-378">Default Value</span></span> | <span data-ttu-id="6f0b5-379">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6f0b5-380">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-380">30 seconds</span></span> | <span data-ttu-id="6f0b5-381">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6f0b5-382">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6f0b5-383">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-384">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-384">15 seconds</span></span> | <span data-ttu-id="6f0b5-385">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6f0b5-386">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-387">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-388">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-388">15 seconds</span></span> | <span data-ttu-id="6f0b5-389">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6f0b5-390">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6f0b5-391">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6f0b5-392">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6f0b5-392">All installed protocols</span></span> | <span data-ttu-id="6f0b5-393">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-393">Protocols supported by this hub.</span></span> <span data-ttu-id="6f0b5-394">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6f0b5-395">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6f0b5-396">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6f0b5-397">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6f0b5-398">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6f0b5-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-399">32 KB</span></span> | <span data-ttu-id="6f0b5-400">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6f0b5-401">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6f0b5-402">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6f0b5-403">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6f0b5-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6f0b5-404">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6f0b5-405">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6f0b5-406">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6f0b5-407">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-407">Option</span></span> | <span data-ttu-id="6f0b5-408">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-408">Default Value</span></span> | <span data-ttu-id="6f0b5-409">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6f0b5-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-410">32 KB</span></span> | <span data-ttu-id="6f0b5-411">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6f0b5-412">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6f0b5-413">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6f0b5-414">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6f0b5-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-415">32 KB</span></span> | <span data-ttu-id="6f0b5-416">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6f0b5-417">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6f0b5-418">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-418">All Transports are enabled.</span></span> | <span data-ttu-id="6f0b5-419">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6f0b5-420">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-420">See below.</span></span> | <span data-ttu-id="6f0b5-421">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6f0b5-422">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-422">See below.</span></span> | <span data-ttu-id="6f0b5-423">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6f0b5-424">0</span><span class="sxs-lookup"><span data-stu-id="6f0b5-424">0</span></span> | <span data-ttu-id="6f0b5-425">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6f0b5-426">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6f0b5-427">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6f0b5-428">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-428">Option</span></span> | <span data-ttu-id="6f0b5-429">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-429">Default Value</span></span> | <span data-ttu-id="6f0b5-430">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6f0b5-431">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-431">90 seconds</span></span> | <span data-ttu-id="6f0b5-432">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6f0b5-433">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6f0b5-434">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6f0b5-435">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-435">Option</span></span> | <span data-ttu-id="6f0b5-436">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-436">Default Value</span></span> | <span data-ttu-id="6f0b5-437">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6f0b5-438">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-438">5 seconds</span></span> | <span data-ttu-id="6f0b5-439">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6f0b5-440">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6f0b5-441">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6f0b5-442">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6f0b5-442">Configure client options</span></span>

<span data-ttu-id="6f0b5-443">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6f0b5-444">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6f0b5-445">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6f0b5-445">Configure logging</span></span>

<span data-ttu-id="6f0b5-446">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6f0b5-447">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6f0b5-448">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-449">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6f0b5-450">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6f0b5-451">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6f0b5-452">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6f0b5-453">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6f0b5-454">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6f0b5-455">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6f0b5-456">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6f0b5-457">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6f0b5-458">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-458">The following table lists the available log levels.</span></span> <span data-ttu-id="6f0b5-459">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6f0b5-460">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela** serão registrados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="6f0b5-461">String</span><span class="sxs-lookup"><span data-stu-id="6f0b5-461">String</span></span>                      | <span data-ttu-id="6f0b5-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6f0b5-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6f0b5-463">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6f0b5-464">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6f0b5-465">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6f0b5-466">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6f0b5-467">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6f0b5-468">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6f0b5-469">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6f0b5-470">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6f0b5-471">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6f0b5-472">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-472">Configure allowed transports</span></span>

<span data-ttu-id="6f0b5-473">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6f0b5-474">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6f0b5-475">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-475">All transports are enabled by default.</span></span>

<span data-ttu-id="6f0b5-476">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6f0b5-477">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6f0b5-478">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6f0b5-479">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6f0b5-480">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6f0b5-481">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6f0b5-482">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6f0b5-482">Configure bearer authentication</span></span>

<span data-ttu-id="6f0b5-483">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6f0b5-484">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6f0b5-485">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6f0b5-486">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6f0b5-487">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6f0b5-488">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6f0b5-489">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6f0b5-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6f0b5-491">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6f0b5-492">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6f0b5-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6f0b5-493">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-494">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-495">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-495">Option</span></span> | <span data-ttu-id="6f0b5-496">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-496">Default value</span></span> | <span data-ttu-id="6f0b5-497">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6f0b5-498">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-499">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-499">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-500">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-501">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-502">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-503">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-503">15 seconds</span></span> | <span data-ttu-id="6f0b5-504">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-505">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-506">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-507">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-508">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-508">15 seconds</span></span> | <span data-ttu-id="6f0b5-509">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-510">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-511">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6f0b5-512">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-514">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-514">Option</span></span> | <span data-ttu-id="6f0b5-515">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-515">Default value</span></span> | <span data-ttu-id="6f0b5-516">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6f0b5-517">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-518">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-518">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-519">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6f0b5-520">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-521">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6f0b5-522">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-523">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-524">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-525">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-526">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-526">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-527">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-527">Option</span></span> | <span data-ttu-id="6f0b5-528">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-528">Default value</span></span> | <span data-ttu-id="6f0b5-529">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6f0b5-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6f0b5-531">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-532">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-532">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-533">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-534">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-535">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6f0b5-536">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-536">15 seconds</span></span> | <span data-ttu-id="6f0b5-537">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-538">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-539">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-540">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6f0b5-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6f0b5-542">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-543">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-544">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-545">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6f0b5-546">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-546">Configure additional options</span></span>

<span data-ttu-id="6f0b5-547">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-548">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-549">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-549">.NET Option</span></span> |  <span data-ttu-id="6f0b5-550">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-550">Default value</span></span> | <span data-ttu-id="6f0b5-551">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-552">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6f0b5-553">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-554">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-555">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6f0b5-556">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-556">Empty</span></span> | <span data-ttu-id="6f0b5-557">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6f0b5-558">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-558">Empty</span></span> | <span data-ttu-id="6f0b5-559">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6f0b5-560">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-560">Empty</span></span> | <span data-ttu-id="6f0b5-561">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6f0b5-562">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-562">5 seconds</span></span> | <span data-ttu-id="6f0b5-563">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-563">WebSockets only.</span></span> <span data-ttu-id="6f0b5-564">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6f0b5-565">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6f0b5-566">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-566">Empty</span></span> | <span data-ttu-id="6f0b5-567">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6f0b5-568">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6f0b5-569">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="6f0b5-570">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6f0b5-571">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6f0b5-572">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6f0b5-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6f0b5-573">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6f0b5-574">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6f0b5-575">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6f0b5-576">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6f0b5-577">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-579">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-579">JavaScript Option</span></span> | <span data-ttu-id="6f0b5-580">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-580">Default Value</span></span> | <span data-ttu-id="6f0b5-581">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6f0b5-582">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="6f0b5-583">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6f0b5-584">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6f0b5-585">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-586">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-587">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-588">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-588">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-589">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-589">Java Option</span></span> | <span data-ttu-id="6f0b5-590">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-590">Default Value</span></span> | <span data-ttu-id="6f0b5-591">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-592">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6f0b5-593">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-594">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-595">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6f0b5-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6f0b5-597">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-597">Empty</span></span> | <span data-ttu-id="6f0b5-598">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6f0b5-599">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6f0b5-600">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6f0b5-601">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6f0b5-602">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6f0b5-603">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-604">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6f0b5-605">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6f0b5-606">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6f0b5-607">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f0b5-608">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6f0b5-609">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6f0b5-610">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6f0b5-611">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="6f0b5-612">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6f0b5-613">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6f0b5-614">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6f0b5-615">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="6f0b5-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6f0b5-616">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6f0b5-617">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-617">MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-618">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6f0b5-619">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-620">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6f0b5-621">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6f0b5-621">Configure server options</span></span>

<span data-ttu-id="6f0b5-622">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6f0b5-623">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-623">Option</span></span> | <span data-ttu-id="6f0b5-624">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-624">Default Value</span></span> | <span data-ttu-id="6f0b5-625">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6f0b5-626">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-626">30 seconds</span></span> | <span data-ttu-id="6f0b5-627">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6f0b5-628">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6f0b5-629">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-630">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-630">15 seconds</span></span> | <span data-ttu-id="6f0b5-631">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6f0b5-632">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-633">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-634">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-634">15 seconds</span></span> | <span data-ttu-id="6f0b5-635">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6f0b5-636">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6f0b5-637">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6f0b5-638">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6f0b5-638">All installed protocols</span></span> | <span data-ttu-id="6f0b5-639">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-639">Protocols supported by this hub.</span></span> <span data-ttu-id="6f0b5-640">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6f0b5-641">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6f0b5-642">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6f0b5-643">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6f0b5-644">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6f0b5-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-645">32 KB</span></span> | <span data-ttu-id="6f0b5-646">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6f0b5-647">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6f0b5-648">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6f0b5-649">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6f0b5-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6f0b5-650">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6f0b5-651">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6f0b5-652">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6f0b5-653">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-653">Option</span></span> | <span data-ttu-id="6f0b5-654">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-654">Default Value</span></span> | <span data-ttu-id="6f0b5-655">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6f0b5-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-656">32 KB</span></span> | <span data-ttu-id="6f0b5-657">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6f0b5-658">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6f0b5-659">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6f0b5-660">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6f0b5-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-661">32 KB</span></span> | <span data-ttu-id="6f0b5-662">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6f0b5-663">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6f0b5-664">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-664">All Transports are enabled.</span></span> | <span data-ttu-id="6f0b5-665">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6f0b5-666">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-666">See below.</span></span> | <span data-ttu-id="6f0b5-667">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6f0b5-668">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-668">See below.</span></span> | <span data-ttu-id="6f0b5-669">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6f0b5-670">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6f0b5-671">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-671">Option</span></span> | <span data-ttu-id="6f0b5-672">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-672">Default Value</span></span> | <span data-ttu-id="6f0b5-673">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6f0b5-674">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-674">90 seconds</span></span> | <span data-ttu-id="6f0b5-675">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6f0b5-676">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6f0b5-677">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6f0b5-678">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-678">Option</span></span> | <span data-ttu-id="6f0b5-679">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-679">Default Value</span></span> | <span data-ttu-id="6f0b5-680">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6f0b5-681">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-681">5 seconds</span></span> | <span data-ttu-id="6f0b5-682">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6f0b5-683">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6f0b5-684">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6f0b5-685">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6f0b5-685">Configure client options</span></span>

<span data-ttu-id="6f0b5-686">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6f0b5-687">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6f0b5-688">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6f0b5-688">Configure logging</span></span>

<span data-ttu-id="6f0b5-689">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6f0b5-690">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6f0b5-691">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-692">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6f0b5-693">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6f0b5-694">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6f0b5-695">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6f0b5-696">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6f0b5-697">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6f0b5-698">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6f0b5-699">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6f0b5-700">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6f0b5-701">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-701">The following table lists the available log levels.</span></span> <span data-ttu-id="6f0b5-702">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6f0b5-703">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela** serão registrados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="6f0b5-704">String</span><span class="sxs-lookup"><span data-stu-id="6f0b5-704">String</span></span>                      | <span data-ttu-id="6f0b5-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6f0b5-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6f0b5-706">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6f0b5-707">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6f0b5-708">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6f0b5-709">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6f0b5-710">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6f0b5-711">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6f0b5-712">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6f0b5-713">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6f0b5-714">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6f0b5-715">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-715">Configure allowed transports</span></span>

<span data-ttu-id="6f0b5-716">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6f0b5-717">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6f0b5-718">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-718">All transports are enabled by default.</span></span>

<span data-ttu-id="6f0b5-719">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6f0b5-720">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6f0b5-721">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6f0b5-722">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6f0b5-723">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6f0b5-724">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6f0b5-725">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6f0b5-725">Configure bearer authentication</span></span>

<span data-ttu-id="6f0b5-726">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6f0b5-727">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6f0b5-728">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6f0b5-729">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6f0b5-730">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6f0b5-731">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6f0b5-732">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6f0b5-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6f0b5-734">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6f0b5-735">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6f0b5-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6f0b5-736">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-737">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-738">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-738">Option</span></span> | <span data-ttu-id="6f0b5-739">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-739">Default value</span></span> | <span data-ttu-id="6f0b5-740">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6f0b5-741">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-742">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-742">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-743">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-744">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-745">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-746">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-746">15 seconds</span></span> | <span data-ttu-id="6f0b5-747">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-748">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-749">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-750">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-751">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-751">15 seconds</span></span> | <span data-ttu-id="6f0b5-752">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-753">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-754">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6f0b5-755">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-757">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-757">Option</span></span> | <span data-ttu-id="6f0b5-758">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-758">Default value</span></span> | <span data-ttu-id="6f0b5-759">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6f0b5-760">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-761">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-761">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-762">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6f0b5-763">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-764">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6f0b5-765">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-766">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-767">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-768">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-769">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-769">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-770">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-770">Option</span></span> | <span data-ttu-id="6f0b5-771">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-771">Default value</span></span> | <span data-ttu-id="6f0b5-772">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6f0b5-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6f0b5-774">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-775">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-775">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-776">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-777">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-778">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6f0b5-779">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-779">15 seconds</span></span> | <span data-ttu-id="6f0b5-780">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-781">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-782">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-783">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6f0b5-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6f0b5-785">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-786">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-787">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-788">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6f0b5-789">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-789">Configure additional options</span></span>

<span data-ttu-id="6f0b5-790">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-791">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-792">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-792">.NET Option</span></span> |  <span data-ttu-id="6f0b5-793">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-793">Default value</span></span> | <span data-ttu-id="6f0b5-794">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-795">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6f0b5-796">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-797">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-798">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6f0b5-799">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-799">Empty</span></span> | <span data-ttu-id="6f0b5-800">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6f0b5-801">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-801">Empty</span></span> | <span data-ttu-id="6f0b5-802">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6f0b5-803">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-803">Empty</span></span> | <span data-ttu-id="6f0b5-804">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6f0b5-805">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-805">5 seconds</span></span> | <span data-ttu-id="6f0b5-806">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-806">WebSockets only.</span></span> <span data-ttu-id="6f0b5-807">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6f0b5-808">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6f0b5-809">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-809">Empty</span></span> | <span data-ttu-id="6f0b5-810">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6f0b5-811">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6f0b5-812">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="6f0b5-813">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6f0b5-814">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6f0b5-815">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6f0b5-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6f0b5-816">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6f0b5-817">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6f0b5-818">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6f0b5-819">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6f0b5-820">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-822">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-822">JavaScript Option</span></span> | <span data-ttu-id="6f0b5-823">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-823">Default Value</span></span> | <span data-ttu-id="6f0b5-824">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6f0b5-825">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="6f0b5-826">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6f0b5-827">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6f0b5-828">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-829">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-830">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-831">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-831">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-832">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-832">Java Option</span></span> | <span data-ttu-id="6f0b5-833">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-833">Default Value</span></span> | <span data-ttu-id="6f0b5-834">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-835">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6f0b5-836">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-837">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-838">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6f0b5-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6f0b5-840">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-840">Empty</span></span> | <span data-ttu-id="6f0b5-841">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6f0b5-842">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6f0b5-843">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6f0b5-844">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6f0b5-845">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6f0b5-846">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-847">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6f0b5-848">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6f0b5-849">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6f0b5-850">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6f0b5-851">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6f0b5-852">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6f0b5-853">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6f0b5-854">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6f0b5-855">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6f0b5-856">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6f0b5-857">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-857">MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-858">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6f0b5-859">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-860">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6f0b5-861">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6f0b5-861">Configure server options</span></span>

<span data-ttu-id="6f0b5-862">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6f0b5-863">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-863">Option</span></span> | <span data-ttu-id="6f0b5-864">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-864">Default Value</span></span> | <span data-ttu-id="6f0b5-865">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6f0b5-866">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-866">30 seconds</span></span> | <span data-ttu-id="6f0b5-867">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6f0b5-868">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6f0b5-869">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-870">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-870">15 seconds</span></span> | <span data-ttu-id="6f0b5-871">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6f0b5-872">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-873">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-874">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-874">15 seconds</span></span> | <span data-ttu-id="6f0b5-875">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6f0b5-876">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6f0b5-877">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6f0b5-878">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6f0b5-878">All installed protocols</span></span> | <span data-ttu-id="6f0b5-879">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-879">Protocols supported by this hub.</span></span> <span data-ttu-id="6f0b5-880">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6f0b5-881">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6f0b5-882">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6f0b5-883">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6f0b5-884">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6f0b5-885">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6f0b5-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6f0b5-886">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6f0b5-887">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6f0b5-888">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6f0b5-889">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-889">Option</span></span> | <span data-ttu-id="6f0b5-890">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-890">Default Value</span></span> | <span data-ttu-id="6f0b5-891">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6f0b5-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-892">32 KB</span></span> | <span data-ttu-id="6f0b5-893">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6f0b5-894">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6f0b5-895">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6f0b5-896">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6f0b5-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-897">32 KB</span></span> | <span data-ttu-id="6f0b5-898">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6f0b5-899">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6f0b5-900">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-900">All Transports are enabled.</span></span> | <span data-ttu-id="6f0b5-901">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6f0b5-902">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-902">See below.</span></span> | <span data-ttu-id="6f0b5-903">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6f0b5-904">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-904">See below.</span></span> | <span data-ttu-id="6f0b5-905">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6f0b5-906">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6f0b5-907">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-907">Option</span></span> | <span data-ttu-id="6f0b5-908">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-908">Default Value</span></span> | <span data-ttu-id="6f0b5-909">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6f0b5-910">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-910">90 seconds</span></span> | <span data-ttu-id="6f0b5-911">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6f0b5-912">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6f0b5-913">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6f0b5-914">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-914">Option</span></span> | <span data-ttu-id="6f0b5-915">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-915">Default Value</span></span> | <span data-ttu-id="6f0b5-916">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6f0b5-917">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-917">5 seconds</span></span> | <span data-ttu-id="6f0b5-918">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6f0b5-919">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6f0b5-920">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6f0b5-921">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6f0b5-921">Configure client options</span></span>

<span data-ttu-id="6f0b5-922">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6f0b5-923">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6f0b5-924">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6f0b5-924">Configure logging</span></span>

<span data-ttu-id="6f0b5-925">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6f0b5-926">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6f0b5-927">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-928">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6f0b5-929">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6f0b5-930">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6f0b5-931">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6f0b5-932">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6f0b5-933">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6f0b5-934">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6f0b5-935">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6f0b5-936">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6f0b5-937">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6f0b5-938">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6f0b5-939">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6f0b5-940">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6f0b5-941">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6f0b5-942">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-942">Configure allowed transports</span></span>

<span data-ttu-id="6f0b5-943">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6f0b5-944">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6f0b5-945">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-945">All transports are enabled by default.</span></span>

<span data-ttu-id="6f0b5-946">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6f0b5-947">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6f0b5-948">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6f0b5-949">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6f0b5-949">Configure bearer authentication</span></span>

<span data-ttu-id="6f0b5-950">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6f0b5-951">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6f0b5-952">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6f0b5-953">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6f0b5-954">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6f0b5-955">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6f0b5-956">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6f0b5-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6f0b5-958">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6f0b5-959">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6f0b5-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6f0b5-960">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-961">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-962">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-962">Option</span></span> | <span data-ttu-id="6f0b5-963">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-963">Default value</span></span> | <span data-ttu-id="6f0b5-964">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6f0b5-965">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-966">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-966">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-967">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-968">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-969">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-970">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-970">15 seconds</span></span> | <span data-ttu-id="6f0b5-971">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-972">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-973">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-974">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-975">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-975">15 seconds</span></span> | <span data-ttu-id="6f0b5-976">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-977">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-978">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6f0b5-979">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-981">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-981">Option</span></span> | <span data-ttu-id="6f0b5-982">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-982">Default value</span></span> | <span data-ttu-id="6f0b5-983">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6f0b5-984">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-985">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-985">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-986">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6f0b5-987">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-988">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6f0b5-989">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-990">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-991">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-992">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-993">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-993">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-994">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-994">Option</span></span> | <span data-ttu-id="6f0b5-995">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-995">Default value</span></span> | <span data-ttu-id="6f0b5-996">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6f0b5-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6f0b5-998">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-999">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-999">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-1000">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-1001">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-1002">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6f0b5-1003">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1003">15 seconds</span></span> | <span data-ttu-id="6f0b5-1004">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-1005">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-1006">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-1007">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6f0b5-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6f0b5-1009">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-1010">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6f0b5-1011">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6f0b5-1012">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6f0b5-1013">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1013">Configure additional options</span></span>

<span data-ttu-id="6f0b5-1014">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-1016">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1016">.NET Option</span></span> |  <span data-ttu-id="6f0b5-1017">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1017">Default value</span></span> | <span data-ttu-id="6f0b5-1018">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-1019">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6f0b5-1020">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-1021">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-1022">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6f0b5-1023">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1023">Empty</span></span> | <span data-ttu-id="6f0b5-1024">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6f0b5-1025">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1025">Empty</span></span> | <span data-ttu-id="6f0b5-1026">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6f0b5-1027">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1027">Empty</span></span> | <span data-ttu-id="6f0b5-1028">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6f0b5-1029">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1029">5 seconds</span></span> | <span data-ttu-id="6f0b5-1030">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1030">WebSockets only.</span></span> <span data-ttu-id="6f0b5-1031">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6f0b5-1032">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6f0b5-1033">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1033">Empty</span></span> | <span data-ttu-id="6f0b5-1034">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6f0b5-1035">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6f0b5-1036">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="6f0b5-1037">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6f0b5-1038">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6f0b5-1039">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6f0b5-1040">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6f0b5-1041">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6f0b5-1042">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6f0b5-1043">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6f0b5-1044">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-1046">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1046">JavaScript Option</span></span> | <span data-ttu-id="6f0b5-1047">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1047">Default Value</span></span> | <span data-ttu-id="6f0b5-1048">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6f0b5-1049">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="6f0b5-1050">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6f0b5-1051">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6f0b5-1052">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-1053">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-1054">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-1055">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-1056">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1056">Java Option</span></span> | <span data-ttu-id="6f0b5-1057">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1057">Default Value</span></span> | <span data-ttu-id="6f0b5-1058">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-1059">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6f0b5-1060">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-1061">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-1062">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6f0b5-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6f0b5-1064">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1064">Empty</span></span> | <span data-ttu-id="6f0b5-1065">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6f0b5-1066">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6f0b5-1067">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6f0b5-1068">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6f0b5-1069">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6f0b5-1070">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-1071">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6f0b5-1072">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6f0b5-1073">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6f0b5-1074">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6f0b5-1075">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6f0b5-1076">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6f0b5-1077">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6f0b5-1078">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6f0b5-1079">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6f0b5-1080">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6f0b5-1081">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1081">MessagePack serialization options</span></span>

<span data-ttu-id="6f0b5-1082">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6f0b5-1083">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-1084">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6f0b5-1085">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1085">Configure server options</span></span>

<span data-ttu-id="6f0b5-1086">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6f0b5-1087">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1087">Option</span></span> | <span data-ttu-id="6f0b5-1088">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1088">Default Value</span></span> | <span data-ttu-id="6f0b5-1089">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-1090">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1090">15 seconds</span></span> | <span data-ttu-id="6f0b5-1091">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6f0b5-1092">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-1093">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6f0b5-1094">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1094">15 seconds</span></span> | <span data-ttu-id="6f0b5-1095">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6f0b5-1096">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6f0b5-1097">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6f0b5-1098">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1098">All installed protocols</span></span> | <span data-ttu-id="6f0b5-1099">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="6f0b5-1100">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6f0b5-1101">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6f0b5-1102">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6f0b5-1103">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6f0b5-1104">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6f0b5-1105">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6f0b5-1106">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6f0b5-1107">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6f0b5-1108">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6f0b5-1109">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1109">Option</span></span> | <span data-ttu-id="6f0b5-1110">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1110">Default Value</span></span> | <span data-ttu-id="6f0b5-1111">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6f0b5-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1112">32 KB</span></span> | <span data-ttu-id="6f0b5-1113">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6f0b5-1114">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6f0b5-1115">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6f0b5-1116">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6f0b5-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1117">32 KB</span></span> | <span data-ttu-id="6f0b5-1118">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6f0b5-1119">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6f0b5-1120">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1120">All Transports are enabled.</span></span> | <span data-ttu-id="6f0b5-1121">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6f0b5-1122">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1122">See below.</span></span> | <span data-ttu-id="6f0b5-1123">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6f0b5-1124">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1124">See below.</span></span> | <span data-ttu-id="6f0b5-1125">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6f0b5-1126">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6f0b5-1127">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1127">Option</span></span> | <span data-ttu-id="6f0b5-1128">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1128">Default Value</span></span> | <span data-ttu-id="6f0b5-1129">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6f0b5-1130">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1130">90 seconds</span></span> | <span data-ttu-id="6f0b5-1131">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6f0b5-1132">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6f0b5-1133">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6f0b5-1134">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1134">Option</span></span> | <span data-ttu-id="6f0b5-1135">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1135">Default Value</span></span> | <span data-ttu-id="6f0b5-1136">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6f0b5-1137">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1137">5 seconds</span></span> | <span data-ttu-id="6f0b5-1138">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6f0b5-1139">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6f0b5-1140">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6f0b5-1141">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1141">Configure client options</span></span>

<span data-ttu-id="6f0b5-1142">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6f0b5-1143">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6f0b5-1144">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1144">Configure logging</span></span>

<span data-ttu-id="6f0b5-1145">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6f0b5-1146">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6f0b5-1147">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b5-1148">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6f0b5-1149">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6f0b5-1150">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6f0b5-1151">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6f0b5-1152">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6f0b5-1153">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6f0b5-1154">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6f0b5-1155">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6f0b5-1156">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6f0b5-1157">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6f0b5-1158">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6f0b5-1159">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6f0b5-1160">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6f0b5-1161">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6f0b5-1162">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1162">Configure allowed transports</span></span>

<span data-ttu-id="6f0b5-1163">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6f0b5-1164">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6f0b5-1165">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="6f0b5-1166">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6f0b5-1167">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6f0b5-1168">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1168">Configure bearer authentication</span></span>

<span data-ttu-id="6f0b5-1169">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6f0b5-1170">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6f0b5-1171">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6f0b5-1172">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6f0b5-1173">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6f0b5-1174">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6f0b5-1175">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6f0b5-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6f0b5-1177">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6f0b5-1178">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6f0b5-1179">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-1181">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1181">Option</span></span> | <span data-ttu-id="6f0b5-1182">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1182">Default value</span></span> | <span data-ttu-id="6f0b5-1183">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6f0b5-1184">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-1185">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1185">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-1186">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-1187">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-1188">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6f0b5-1189">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1189">15 seconds</span></span> | <span data-ttu-id="6f0b5-1190">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-1191">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6f0b5-1192">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-1193">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="6f0b5-1194">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-1196">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1196">Option</span></span> | <span data-ttu-id="6f0b5-1197">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1197">Default value</span></span> | <span data-ttu-id="6f0b5-1198">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6f0b5-1199">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-1200">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1200">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-1201">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6f0b5-1202">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-1203">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-1204">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-1205">Opção</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1205">Option</span></span> | <span data-ttu-id="6f0b5-1206">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1206">Default value</span></span> | <span data-ttu-id="6f0b5-1207">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6f0b5-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6f0b5-1209">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6f0b5-1210">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1210">Timeout for server activity.</span></span> <span data-ttu-id="6f0b5-1211">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-1212">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6f0b5-1213">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` , para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6f0b5-1214">15 s</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1214">15 seconds</span></span> | <span data-ttu-id="6f0b5-1215">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="6f0b5-1216">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6f0b5-1217">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6f0b5-1218">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6f0b5-1219">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1219">Configure additional options</span></span>

<span data-ttu-id="6f0b5-1220">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6f0b5-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6f0b5-1222">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1222">.NET Option</span></span> |  <span data-ttu-id="6f0b5-1223">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1223">Default value</span></span> | <span data-ttu-id="6f0b5-1224">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-1225">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6f0b5-1226">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-1227">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-1228">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6f0b5-1229">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1229">Empty</span></span> | <span data-ttu-id="6f0b5-1230">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6f0b5-1231">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1231">Empty</span></span> | <span data-ttu-id="6f0b5-1232">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6f0b5-1233">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1233">Empty</span></span> | <span data-ttu-id="6f0b5-1234">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6f0b5-1235">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1235">5 seconds</span></span> | <span data-ttu-id="6f0b5-1236">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1236">WebSockets only.</span></span> <span data-ttu-id="6f0b5-1237">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6f0b5-1238">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6f0b5-1239">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1239">Empty</span></span> | <span data-ttu-id="6f0b5-1240">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6f0b5-1241">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6f0b5-1242">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="6f0b5-1243">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6f0b5-1244">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6f0b5-1245">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6f0b5-1246">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6f0b5-1247">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6f0b5-1248">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6f0b5-1249">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6f0b5-1250">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6f0b5-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6f0b5-1252">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1252">JavaScript Option</span></span> | <span data-ttu-id="6f0b5-1253">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1253">Default Value</span></span> | <span data-ttu-id="6f0b5-1254">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6f0b5-1255">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="6f0b5-1256">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6f0b5-1257">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6f0b5-1258">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-1259">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-1260">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6f0b5-1261">Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="6f0b5-1262">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1262">Java Option</span></span> | <span data-ttu-id="6f0b5-1263">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1263">Default Value</span></span> | <span data-ttu-id="6f0b5-1264">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6f0b5-1265">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6f0b5-1266">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6f0b5-1267">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="6f0b5-1268">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6f0b5-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6f0b5-1270">Vazio</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1270">Empty</span></span> | <span data-ttu-id="6f0b5-1271">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6f0b5-1272">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6f0b5-1273">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6f0b5-1274">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6f0b5-1275">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6f0b5-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
