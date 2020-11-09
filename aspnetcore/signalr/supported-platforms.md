---
title: 'ASP.NET Core SignalR plataformas com suporte'
author: bradygaster
description: 'Saiba mais sobre as plataformas com suporte para ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051012"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="a40bd-103">ASP.NET Core SignalR plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="a40bd-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="a40bd-104">Requisitos do sistema do servidor do</span><span class="sxs-lookup"><span data-stu-id="a40bd-104">Server system requirements</span></span>

<span data-ttu-id="a40bd-105">SignalR para ASP.NET Core dá suporte a qualquer plataforma de servidor que ASP.NET Core suporte.</span><span class="sxs-lookup"><span data-stu-id="a40bd-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="a40bd-106">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="a40bd-106">JavaScript client</span></span>

<span data-ttu-id="a40bd-107">O [cliente JavaScript](xref:signalr/javascript-client) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="a40bd-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="a40bd-108">Navegador</span><span class="sxs-lookup"><span data-stu-id="a40bd-108">Browser</span></span>                          | <span data-ttu-id="a40bd-109">Versão</span><span class="sxs-lookup"><span data-stu-id="a40bd-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="a40bd-110">Apple Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="a40bd-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="a40bd-111">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a40bd-111">Current&dagger;</span></span> |
| <span data-ttu-id="a40bd-112">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="a40bd-112">Google Chrome, including Android</span></span> | <span data-ttu-id="a40bd-113">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a40bd-113">Current&dagger;</span></span> |
| <span data-ttu-id="a40bd-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a40bd-114">Microsoft Edge</span></span>                   | <span data-ttu-id="a40bd-115">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a40bd-115">Current&dagger;</span></span> |
| <span data-ttu-id="a40bd-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="a40bd-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="a40bd-117">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a40bd-117">Current&dagger;</span></span> |

<span data-ttu-id="a40bd-118">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="a40bd-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="a40bd-119">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="a40bd-119">.NET client</span></span>

<span data-ttu-id="a40bd-120">O [cliente .net](xref:signalr/dotnet-client) é executado em qualquer plataforma com suporte do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a40bd-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="a40bd-121">Por exemplo, [os desenvolvedores do xamarin SignalR podem usar](https://github.com/aspnet/Announcements/issues/305) o para criar aplicativos Android usando xamarin. Android 8.4.0.1 e posterior e aplicativos Ios usando xamarin. Ios 11.14.0.4 e posterior.</span><span class="sxs-lookup"><span data-stu-id="a40bd-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="a40bd-122">Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="a40bd-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="a40bd-123">Outros transportes têm suporte em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="a40bd-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="a40bd-124">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="a40bd-124">Java client</span></span>

<span data-ttu-id="a40bd-125">O [cliente Java](xref:signalr/java-client) dá suporte a Java 8 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="a40bd-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="a40bd-126">Clientes sem suporte</span><span class="sxs-lookup"><span data-stu-id="a40bd-126">Unsupported clients</span></span>

<span data-ttu-id="a40bd-127">Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais.</span><span class="sxs-lookup"><span data-stu-id="a40bd-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="a40bd-128">Atualmente, eles não têm suporte e podem nunca ser.</span><span class="sxs-lookup"><span data-stu-id="a40bd-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="a40bd-129">[Cliente C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="a40bd-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="a40bd-130">[Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="a40bd-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
