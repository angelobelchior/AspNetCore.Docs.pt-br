---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: 'Saiba como usar o serviço de ASP.NET Core :::no-loc(SignalR)::: HubContext para enviar notificações para clientes de fora de um Hub.'
monikerRange: '>= aspnetcore-2.1'
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058174"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="05c1d-103">Enviar mensagens de fora de um hub</span><span class="sxs-lookup"><span data-stu-id="05c1d-103">Send messages from outside a hub</span></span>

<span data-ttu-id="05c1d-104">Por [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="05c1d-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="05c1d-105">O :::no-loc(SignalR)::: Hub é a abstração principal para o envio de mensagens aos clientes conectados ao :::no-loc(SignalR)::: servidor.</span><span class="sxs-lookup"><span data-stu-id="05c1d-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="05c1d-106">Também é possível enviar mensagens de outros locais em seu aplicativo usando o `IHubContext` serviço.</span><span class="sxs-lookup"><span data-stu-id="05c1d-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="05c1d-107">Este artigo explica como acessar um :::no-loc(SignalR)::: `IHubContext` para enviar notificações para clientes de fora de um Hub.</span><span class="sxs-lookup"><span data-stu-id="05c1d-107">This article explains how to access a :::no-loc(SignalR)::: `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="05c1d-108">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="05c1d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="05c1d-109">Obter uma instância de IHubContext</span><span class="sxs-lookup"><span data-stu-id="05c1d-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="05c1d-110">No ASP.NET Core :::no-loc(SignalR)::: , você pode acessar uma instância do `IHubContext` por meio de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="05c1d-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="05c1d-111">Você pode injetar uma instância do `IHubContext` em um controlador, middleware ou outro serviço de di.</span><span class="sxs-lookup"><span data-stu-id="05c1d-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="05c1d-112">Use a instância do para enviar mensagens aos clientes.</span><span class="sxs-lookup"><span data-stu-id="05c1d-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="05c1d-113">Isso difere do ASP.NET 4. x, :::no-loc(SignalR)::: que usava GlobalHost para fornecer acesso ao `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="05c1d-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="05c1d-114">ASP.NET Core tem uma estrutura de injeção de dependência que elimina a necessidade desse singleton global.</span><span class="sxs-lookup"><span data-stu-id="05c1d-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="05c1d-115">Injetar uma instância de IHubContext em um controlador</span><span class="sxs-lookup"><span data-stu-id="05c1d-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="05c1d-116">Você pode injetar uma instância do `IHubContext` em um controlador adicionando-a ao seu construtor:</span><span class="sxs-lookup"><span data-stu-id="05c1d-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="05c1d-117">Agora, com acesso a uma instância do `IHubContext` , você pode chamar métodos de Hub como se estivesse no próprio Hub.</span><span class="sxs-lookup"><span data-stu-id="05c1d-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="05c1d-118">Obter uma instância de IHubContext no middleware</span><span class="sxs-lookup"><span data-stu-id="05c1d-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="05c1d-119">Acesse o `IHubContext` no pipeline de middleware da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="05c1d-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="05c1d-120">Quando os métodos de Hub são chamados de fora da `Hub` classe, não há nenhum chamador associado à invocação.</span><span class="sxs-lookup"><span data-stu-id="05c1d-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="05c1d-121">Portanto, não há acesso às `ConnectionId` `Caller` Propriedades, e `Others` .</span><span class="sxs-lookup"><span data-stu-id="05c1d-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="05c1d-122">Obter uma instância de IHubContext de IHost</span><span class="sxs-lookup"><span data-stu-id="05c1d-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="05c1d-123">O acesso `IHubContext` a um do host da Web é útil para integração com áreas fora do ASP.NET Core, por exemplo, usando estruturas de injeção de dependência de terceiros:</span><span class="sxs-lookup"><span data-stu-id="05c1d-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="05c1d-124">Injetar um HubContext fortemente tipado</span><span class="sxs-lookup"><span data-stu-id="05c1d-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="05c1d-125">Para injetar um HubContext fortemente tipado, verifique se o seu hub é herdado de `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="05c1d-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="05c1d-126">Insira-o usando a `IHubContext<THub, T>` interface em vez de `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="05c1d-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="05c1d-127">Consulte [hubs com rigidez de tipos](xref:signalr/hubs#strongly-typed-hubs) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="05c1d-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="05c1d-128">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="05c1d-128">Related resources</span></span>

* [<span data-ttu-id="05c1d-129">Introdução</span><span class="sxs-lookup"><span data-stu-id="05c1d-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="05c1d-130">Hubs</span><span class="sxs-lookup"><span data-stu-id="05c1d-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="05c1d-131">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="05c1d-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
