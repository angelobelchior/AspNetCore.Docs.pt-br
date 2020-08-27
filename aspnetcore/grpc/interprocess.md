---
title: Comunicação entre processos com o gRPC
author: jamesnk
description: Saiba como usar o gRPC para comunicação entre processos.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945635"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="23260-103">Comunicação entre processos com o gRPC</span><span class="sxs-lookup"><span data-stu-id="23260-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="23260-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="23260-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="23260-105">chamadas de gRPC entre um cliente e um serviço geralmente são enviadas por Soquetes TCP.</span><span class="sxs-lookup"><span data-stu-id="23260-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="23260-106">O TCP é ótimo para a comunicação em uma rede, mas a [IPC (comunicação entre processos)](https://wikipedia.org/wiki/Inter-process_communication) é mais eficiente quando o cliente e o serviço estão no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="23260-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="23260-107">Este documento explica como usar o gRPC com transportes personalizados em cenários de IPC.</span><span class="sxs-lookup"><span data-stu-id="23260-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="23260-108">Configuração de Servidor</span><span class="sxs-lookup"><span data-stu-id="23260-108">Server configuration</span></span>

<span data-ttu-id="23260-109">Os transportes personalizados têm suporte do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="23260-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="23260-110">Kestrel está configurado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="23260-110">Kestrel is configured in *Program.cs*:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

<span data-ttu-id="23260-111">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="23260-111">The preceding example:</span></span>

* <span data-ttu-id="23260-112">Configura os pontos de extremidade do Kestrel no `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="23260-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="23260-113">Chamadas <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escutar um [soquete de domínio do UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) com o caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="23260-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="23260-114">Kestrel tem suporte interno para pontos de extremidade de UDS.</span><span class="sxs-lookup"><span data-stu-id="23260-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="23260-115">O UDS tem suporte nas versões Linux, macOS e [modernas do Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="23260-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="23260-116">Configuração do cliente</span><span class="sxs-lookup"><span data-stu-id="23260-116">Client configuration</span></span>

<span data-ttu-id="23260-117">`GrpcChannel` dá suporte à realização de chamadas gRPC em transportes personalizados.</span><span class="sxs-lookup"><span data-stu-id="23260-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="23260-118">Quando um canal é criado, ele pode ser configurado com um `SocketsHttpHandler` que tem um personalizado `ConnectionFactory` .</span><span class="sxs-lookup"><span data-stu-id="23260-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="23260-119">A fábrica permite que o cliente faça conexões por meio de transportes personalizados e, em seguida, envie solicitações HTTP nesse transporte.</span><span class="sxs-lookup"><span data-stu-id="23260-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="23260-120">`ConnectionFactory` é uma nova API no .NET 5 Release Candidate 1.</span><span class="sxs-lookup"><span data-stu-id="23260-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="23260-121">Exemplo de fábrica de conexões de soquetes de domínio UNIX:</span><span class="sxs-lookup"><span data-stu-id="23260-121">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

<span data-ttu-id="23260-122">Usando a fábrica de conexões personalizada para criar um canal:</span><span class="sxs-lookup"><span data-stu-id="23260-122">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="23260-123">Os canais criados usando o código anterior enviam chamadas gRPC sobre soquetes de domínio do UNIX.</span><span class="sxs-lookup"><span data-stu-id="23260-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
