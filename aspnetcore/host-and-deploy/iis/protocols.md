---
title: Usar ASP.NET Core com HTTP/2 no IIS
author: rick-anderson
description: Saiba como usar recursos HTTP/2 com o IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057408"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="335bc-103">Usar ASP.NET Core com HTTP/2 no IIS</span><span class="sxs-lookup"><span data-stu-id="335bc-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="335bc-104">De [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="335bc-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="335bc-105">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:</span><span class="sxs-lookup"><span data-stu-id="335bc-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="335bc-106">Windows Server 2016 ou posterior/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="335bc-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="335bc-107">IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="335bc-107">IIS 10 or later</span></span>
* <span data-ttu-id="335bc-108">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="335bc-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="335bc-109">Ao [hospedar fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model): as conexões do servidor de borda voltadas para o público usam http/2, mas a conexão de proxy reverso com o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa http/1.1.</span><span class="sxs-lookup"><span data-stu-id="335bc-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="335bc-110">Para uma implantação em processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="335bc-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="335bc-111">Para uma implantação fora do processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="335bc-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="335bc-112">Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="335bc-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="335bc-113">O HTTP/2 é habilitado por padrão para conexões HTTPS/TLS.</span><span class="sxs-lookup"><span data-stu-id="335bc-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="335bc-114">As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida.</span><span class="sxs-lookup"><span data-stu-id="335bc-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="335bc-115">Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="335bc-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="335bc-116">Recursos avançados de HTTP/2 para dar suporte a gRPC</span><span class="sxs-lookup"><span data-stu-id="335bc-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="335bc-117">Recursos adicionais de HTTP/2 no IIS dão suporte a gRPC, incluindo suporte para os trailers de resposta e envio de quadros de redefinição.</span><span class="sxs-lookup"><span data-stu-id="335bc-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="335bc-118">Requisitos para executar o gRPC no IIS:</span><span class="sxs-lookup"><span data-stu-id="335bc-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="335bc-119">Hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="335bc-119">In-process hosting.</span></span>
* <span data-ttu-id="335bc-120">Windows 10, Build do sistema operacional 20300,1000 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="335bc-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="335bc-121">Pode exigir o uso de compilações do Windows Insider.</span><span class="sxs-lookup"><span data-stu-id="335bc-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="335bc-122">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="335bc-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="335bc-123">Trailers</span><span class="sxs-lookup"><span data-stu-id="335bc-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="335bc-124">Redefinir</span><span class="sxs-lookup"><span data-stu-id="335bc-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
