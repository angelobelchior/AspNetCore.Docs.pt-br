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
ms.openlocfilehash: 7278ebd001aea4ba52c1134b3bac696c01950a27
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722970"
---
# <a name="inter-process-communication-with-grpc"></a>Comunicação entre processos com o gRPC

Por [James Newton – King](https://twitter.com/jamesnk)

chamadas de gRPC entre um cliente e um serviço geralmente são enviadas por Soquetes TCP. O TCP foi projetado para se comunicar em uma rede. A [comunicação entre processos (IPC)](https://wikipedia.org/wiki/Inter-process_communication) é mais eficiente do que o TCP quando o cliente e o serviço estão no mesmo computador. Este documento explica como usar o gRPC com transportes personalizados em cenários de IPC.

## <a name="server-configuration"></a>Configuração de Servidor

Os transportes personalizados têm suporte do [Kestrel](xref:fundamentals/servers/kestrel). Kestrel está configurado em *Program.cs*:

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

O exemplo anterior:

* Configura os pontos de extremidade do Kestrel no `ConfigureKestrel` .
* Chamadas <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escutar um [soquete de domínio do UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) com o caminho especificado.

Kestrel tem suporte interno para pontos de extremidade de UDS. O UDS tem suporte nas versões Linux, macOS e [modernas do Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configuração do cliente

`GrpcChannel` dá suporte à realização de chamadas gRPC em transportes personalizados. Quando um canal é criado, ele pode ser configurado com um `SocketsHttpHandler` que tem um personalizado `ConnectCallback` . O retorno de chamada permite que o cliente faça conexões por meio de transportes personalizados e, em seguida, envie solicitações HTTP nesse transporte.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` é uma nova API no .NET 5 Release Candidate 2.

Exemplo de fábrica de conexões de soquetes de domínio UNIX:

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

Usando a fábrica de conexões personalizada para criar um canal:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Os canais criados usando o código anterior enviam chamadas gRPC sobre soquetes de domínio do UNIX. O suporte para outras tecnologias de IPC pode ser implementado usando a extensibilidade em Kestrel e `SocketsHttpHandler` .
