---
title: Comunicação entre processos com o gRPC
author: jamesnk
description: Saiba como usar o gRPC para comunicação entre processos.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
ms.openlocfilehash: 34876f31cbc51ba66a91ae32ea6a5213dc34a369
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770149"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="73dc8-103">Comunicação entre processos com o gRPC</span><span class="sxs-lookup"><span data-stu-id="73dc8-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="73dc8-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="73dc8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="73dc8-105">chamadas de gRPC entre um cliente e um serviço geralmente são enviadas por Soquetes TCP.</span><span class="sxs-lookup"><span data-stu-id="73dc8-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="73dc8-106">O TCP foi projetado para se comunicar em uma rede.</span><span class="sxs-lookup"><span data-stu-id="73dc8-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="73dc8-107">A [comunicação entre processos (IPC)](https://wikipedia.org/wiki/Inter-process_communication) é mais eficiente do que o TCP quando o cliente e o serviço estão no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="73dc8-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="73dc8-108">Este documento explica como usar o gRPC com transportes personalizados em cenários de IPC.</span><span class="sxs-lookup"><span data-stu-id="73dc8-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="73dc8-109">Configuração de Servidor</span><span class="sxs-lookup"><span data-stu-id="73dc8-109">Server configuration</span></span>

<span data-ttu-id="73dc8-110">Os transportes personalizados têm suporte do [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="73dc8-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="73dc8-111">Kestrel está configurado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="73dc8-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="73dc8-112">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="73dc8-112">The preceding example:</span></span>

* <span data-ttu-id="73dc8-113">Configura os pontos de extremidade do Kestrel no `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="73dc8-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="73dc8-114">Chamadas <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escutar um [soquete de domínio do UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) com o caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="73dc8-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="73dc8-115">Kestrel tem suporte interno para pontos de extremidade de UDS.</span><span class="sxs-lookup"><span data-stu-id="73dc8-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="73dc8-116">O UDS tem suporte nas versões Linux, macOS e [modernas do Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="73dc8-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="73dc8-117">Configuração do cliente</span><span class="sxs-lookup"><span data-stu-id="73dc8-117">Client configuration</span></span>

<span data-ttu-id="73dc8-118">`GrpcChannel` dá suporte à realização de chamadas gRPC em transportes personalizados.</span><span class="sxs-lookup"><span data-stu-id="73dc8-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="73dc8-119">Quando um canal é criado, ele pode ser configurado com um `SocketsHttpHandler` que tem um personalizado `ConnectCallback` .</span><span class="sxs-lookup"><span data-stu-id="73dc8-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="73dc8-120">O retorno de chamada permite que o cliente faça conexões por meio de transportes personalizados e, em seguida, envie solicitações HTTP nesse transporte.</span><span class="sxs-lookup"><span data-stu-id="73dc8-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73dc8-121">`SocketsHttpHandler.ConnectCallback` é uma nova API no .NET 5 Release Candidate 2.</span><span class="sxs-lookup"><span data-stu-id="73dc8-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="73dc8-122">Exemplo de fábrica de conexões de soquetes de domínio UNIX:</span><span class="sxs-lookup"><span data-stu-id="73dc8-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="73dc8-123">Usando a fábrica de conexões personalizada para criar um canal:</span><span class="sxs-lookup"><span data-stu-id="73dc8-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="73dc8-124">Os canais criados usando o código anterior enviam chamadas gRPC sobre soquetes de domínio do UNIX.</span><span class="sxs-lookup"><span data-stu-id="73dc8-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="73dc8-125">O suporte para outras tecnologias de IPC pode ser implementado usando a extensibilidade em Kestrel e `SocketsHttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="73dc8-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>