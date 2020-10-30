---
title: 'ASP.NET Core :::no-loc(SignalR)::: de host em serviços em segundo plano'
author: bradygaster
description: 'Saiba como enviar mensagens para :::no-loc(SignalR)::: clientes de classes BackgroundService do .NET Core.'
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 810eff7ccb08ecc22ea255bf0a9fe3d22637179f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060099"
---
# <a name="host-aspnet-core-no-locsignalr-in-background-services"></a><span data-ttu-id="e6a4c-103">ASP.NET Core :::no-loc(SignalR)::: de host em serviços em segundo plano</span><span class="sxs-lookup"><span data-stu-id="e6a4c-103">Host ASP.NET Core :::no-loc(SignalR)::: in background services</span></span>

<span data-ttu-id="e6a4c-104">Por [Brady GASTER](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="e6a4c-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="e6a4c-105">Este artigo fornece diretrizes para:</span><span class="sxs-lookup"><span data-stu-id="e6a4c-105">This article provides guidance for:</span></span>

* <span data-ttu-id="e6a4c-106">:::no-loc(SignalR):::Hubs de hospedagem usando um processo de trabalho em segundo plano hospedado com ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-106">Hosting :::no-loc(SignalR)::: Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="e6a4c-107">Envio de mensagens para clientes conectados de dentro de um [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6a4c-108">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e6a4c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e6a4c-109">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e6a4c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-no-locsignalr-in-startup"></a><span data-ttu-id="e6a4c-110">Habilitar :::no-loc(SignalR)::: na inicialização</span><span class="sxs-lookup"><span data-stu-id="e6a4c-110">Enable :::no-loc(SignalR)::: in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6a4c-111">Hospedar :::no-loc(SignalR)::: hubs de ASP.NET Core no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-111">Hosting ASP.NET Core :::no-loc(SignalR)::: Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="e6a4c-112">No `Startup.ConfigureServices` método, chamar `services.Add:::no-loc(SignalR):::` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para dar suporte ao :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-112">In the `Startup.ConfigureServices` method, calling `services.Add:::no-loc(SignalR):::` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support :::no-loc(SignalR):::.</span></span> <span data-ttu-id="e6a4c-113">No `Startup.Configure` , o `MapHub` método é chamado no `UseEndpoints` retorno de chamada para conectar os pontos de extremidade do Hub no pipeline de solicitação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e6a4c-114">Hospedar :::no-loc(SignalR)::: hubs de ASP.NET Core no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-114">Hosting ASP.NET Core :::no-loc(SignalR)::: Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="e6a4c-115">No `Startup.ConfigureServices` método, chamar `services.Add:::no-loc(SignalR):::` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para dar suporte ao :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-115">In the `Startup.ConfigureServices` method, calling `services.Add:::no-loc(SignalR):::` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support :::no-loc(SignalR):::.</span></span> <span data-ttu-id="e6a4c-116">No `Startup.Configure` , o `Use:::no-loc(SignalR):::` método é chamado para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-116">In `Startup.Configure`, the `Use:::no-loc(SignalR):::` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="e6a4c-117">No exemplo anterior, a `ClockHub` classe implementa a `Hub<T>` classe para criar um hub fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="e6a4c-118">O `ClockHub` foi configurado na `Startup` classe para responder às solicitações no ponto de extremidade `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="e6a4c-119">Para obter mais informações sobre hubs com rigidez de tipos, consulte [usar hubs no :::no-loc(SignalR)::: para ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="e6a4c-119">For more information on strongly typed Hubs, see [Use hubs in :::no-loc(SignalR)::: for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="e6a4c-120">Essa funcionalidade não é limitada à [classe \<T> Hub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub`1) class.</span></span> <span data-ttu-id="e6a4c-121">Qualquer classe que herda do [Hub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.DynamicHub), funciona.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="e6a4c-122">A interface usada pelo fortemente tipado `ClockHub` é a `IClock` interface.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-no-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="e6a4c-123">Chamar um :::no-loc(SignalR)::: Hub de um serviço em segundo plano</span><span class="sxs-lookup"><span data-stu-id="e6a4c-123">Call a :::no-loc(SignalR)::: Hub from a background service</span></span>

<span data-ttu-id="e6a4c-124">Durante a inicialização, a `Worker` classe, a `BackgroundService` , é habilitada usando `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="e6a4c-125">Como :::no-loc(SignalR)::: o também é habilitado durante a `Startup` fase, na qual cada Hub é anexado a um ponto de extremidade individual no pipeline de solicitação HTTP do ASP.NET Core, cada Hub é representado por um `IHubContext<T>` no servidor.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-125">Since :::no-loc(SignalR)::: is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="e6a4c-126">Usando os recursos de DI do ASP.NET Core, outras classes instanciadas pela camada de hospedagem, como `BackgroundService` classes, classes do controlador MVC ou :::no-loc(Razor)::: modelos de página, podem obter referências a hubs do lado do servidor aceitando instâncias do `IHubContext<ClockHub, IClock>` durante a construção.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or :::no-loc(Razor)::: page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="e6a4c-127">Como o `ExecuteAsync` método é chamado iterativamente no serviço em segundo plano, a data e hora atuais do servidor são enviadas para os clientes conectados usando o `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-no-locsignalr-events-with-background-services"></a><span data-ttu-id="e6a4c-128">Reagir a :::no-loc(SignalR)::: eventos com serviços em segundo plano</span><span class="sxs-lookup"><span data-stu-id="e6a4c-128">React to :::no-loc(SignalR)::: events with background services</span></span>

<span data-ttu-id="e6a4c-129">Como um aplicativo de página única usando o cliente JavaScript para o :::no-loc(SignalR)::: ou um aplicativo de desktop .net pode fazer uso do <xref:signalr/dotnet-client> , a ou a `BackgroundService` `IHostedService` implementação também pode ser usada para conectar-se a :::no-loc(SignalR)::: hubs e responder a eventos.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-129">Like a Single Page App using the JavaScript client for :::no-loc(SignalR)::: or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to :::no-loc(SignalR)::: Hubs and respond to events.</span></span>

<span data-ttu-id="e6a4c-130">A `ClockHubClient` classe implementa a `IClock` interface e a `IHostedService` interface.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="e6a4c-131">Dessa forma, ele pode ser habilitado durante `Startup` a execução contínua e responder a eventos de Hub do servidor.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="e6a4c-132">Durante a inicialização, o `ClockHubClient` cria uma instância de a `HubConnection` e habilita o `IClock.ShowTime` método como o manipulador para o evento do Hub `ShowTime` .</span><span class="sxs-lookup"><span data-stu-id="e6a4c-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="e6a4c-133">Na `IHostedService.StartAsync` implementação, o `HubConnection` é iniciado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="e6a4c-134">Durante o `IHostedService.StopAsync` método, o `HubConnection` é Descartado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="e6a4c-135">Na `IHostedService.StartAsync` implementação, o `HubConnection` é iniciado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="e6a4c-136">Durante o `IHostedService.StopAsync` método, o `HubConnection` é Descartado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e6a4c-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e6a4c-137">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6a4c-137">Additional resources</span></span>

* [<span data-ttu-id="e6a4c-138">Introdução</span><span class="sxs-lookup"><span data-stu-id="e6a4c-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e6a4c-139">Hubs</span><span class="sxs-lookup"><span data-stu-id="e6a4c-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e6a4c-140">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="e6a4c-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="e6a4c-141">Hubs com rigidez de tipos</span><span class="sxs-lookup"><span data-stu-id="e6a4c-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
