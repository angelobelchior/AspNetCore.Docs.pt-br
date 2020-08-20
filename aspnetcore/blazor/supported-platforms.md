---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625941"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="c0c29-103">ASP.NET Core Blazor plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="c0c29-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="c0c29-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c0c29-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="c0c29-105">Requisitos de navegador</span><span class="sxs-lookup"><span data-stu-id="c0c29-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="c0c29-106">Navegador</span><span class="sxs-lookup"><span data-stu-id="c0c29-106">Browser</span></span>                          | <span data-ttu-id="c0c29-107">Versão</span><span class="sxs-lookup"><span data-stu-id="c0c29-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="c0c29-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c0c29-108">Microsoft Edge</span></span>                   | <span data-ttu-id="c0c29-109">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-109">Current</span></span>               |
| <span data-ttu-id="c0c29-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c0c29-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="c0c29-111">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-111">Current</span></span>               |
| <span data-ttu-id="c0c29-112">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="c0c29-112">Google Chrome, including Android</span></span> | <span data-ttu-id="c0c29-113">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-113">Current</span></span>               |
| <span data-ttu-id="c0c29-114">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="c0c29-114">Safari, including iOS</span></span>            | <span data-ttu-id="c0c29-115">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-115">Current</span></span>               |
| <span data-ttu-id="c0c29-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c0c29-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="c0c29-117">Sem suporte&dagger;</span><span class="sxs-lookup"><span data-stu-id="c0c29-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="c0c29-118">&dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="c0c29-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="c0c29-119">Navegador</span><span class="sxs-lookup"><span data-stu-id="c0c29-119">Browser</span></span>                          | <span data-ttu-id="c0c29-120">Versão</span><span class="sxs-lookup"><span data-stu-id="c0c29-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="c0c29-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c0c29-121">Microsoft Edge</span></span>                   | <span data-ttu-id="c0c29-122">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-122">Current</span></span>    |
| <span data-ttu-id="c0c29-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c0c29-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="c0c29-124">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-124">Current</span></span>    |
| <span data-ttu-id="c0c29-125">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="c0c29-125">Google Chrome, including Android</span></span> | <span data-ttu-id="c0c29-126">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-126">Current</span></span>    |
| <span data-ttu-id="c0c29-127">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="c0c29-127">Safari, including iOS</span></span>            | <span data-ttu-id="c0c29-128">Current</span><span class="sxs-lookup"><span data-stu-id="c0c29-128">Current</span></span>    |
| <span data-ttu-id="c0c29-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c0c29-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="c0c29-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="c0c29-130">11&dagger;</span></span> |

<span data-ttu-id="c0c29-131">&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote).</span><span class="sxs-lookup"><span data-stu-id="c0c29-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0c29-132">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c0c29-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
