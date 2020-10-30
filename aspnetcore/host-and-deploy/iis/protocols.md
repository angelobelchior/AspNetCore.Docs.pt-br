---
title: Usar ASP.NET Core com HTTP/2 no IIS
author: rick-anderson
description: Saiba como usar recursos HTTP/2 com o IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057408"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Usar ASP.NET Core com HTTP/2 no IIS

De [Justin Kotalik](https://github.com/jkotalik)

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:

* Windows Server 2016 ou posterior/Windows 10 ou posterior
* IIS 10 ou posterior
* Conexão TLS 1.2 ou posterior
* Ao [hospedar fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model): as conexões do servidor de borda voltadas para o público usam http/2, mas a conexão de proxy reverso com o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa http/1.1.

Para uma implantação em processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/2` . Para uma implantação fora do processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/1.1` .

Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.

O HTTP/2 é habilitado por padrão para conexões HTTPS/TLS. As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida. Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Recursos avançados de HTTP/2 para dar suporte a gRPC

Recursos adicionais de HTTP/2 no IIS dão suporte a gRPC, incluindo suporte para os trailers de resposta e envio de quadros de redefinição.

Requisitos para executar o gRPC no IIS:

* Hospedagem em processo.
* Windows 10, Build do sistema operacional 20300,1000 ou posterior. Pode exigir o uso de compilações do Windows Insider.
* Conexão TLS 1.2 ou posterior

### <a name="trailers"></a>Trailers

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Redefinir

[!INCLUDE[](~/includes/reset.md)]
