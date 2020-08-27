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
# <a name="inter-process-communication-with-grpc"></a>Comunicação entre processos com o gRPC

Por [James Newton – King](https://twitter.com/jamesnk)

chamadas de gRPC entre um cliente e um serviço geralmente são enviadas por Soquetes TCP. O TCP é ótimo para a comunicação em uma rede, mas a [IPC (comunicação entre processos)](https://wikipedia.org/wiki/Inter-process_communication) é mais eficiente quando o cliente e o serviço estão no mesmo computador. Este documento explica como usar o gRPC com transportes personalizados em cenários de IPC.

## <a name="server-configuration"></a>Configuração de Servidor

Os transportes personalizados têm suporte do Kestrel. Kestrel está configurado em *Program.cs*:

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
* Chamadas <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escutar um [soquete de domínio do UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) com o caminho especificado.

Kestrel tem suporte interno para pontos de extremidade de UDS. O UDS tem suporte nas versões Linux, macOS e [modernas do Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configuração do cliente

`GrpcChannel` dá suporte à realização de chamadas gRPC em transportes personalizados. Quando um canal é criado, ele pode ser configurado com um `SocketsHttpHandler` que tem um personalizado `ConnectionFactory` . A fábrica permite que o cliente faça conexões por meio de transportes personalizados e, em seguida, envie solicitações HTTP nesse transporte.

> [!IMPORTANT]
> `ConnectionFactory` é uma nova API no .NET 5 Release Candidate 1.

Exemplo de fábrica de conexões de soquetes de domínio UNIX:

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

Usando a fábrica de conexões personalizada para criar um canal:

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

Os canais criados usando o código anterior enviam chamadas gRPC sobre soquetes de domínio do UNIX.
