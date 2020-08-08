---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013522"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="8b413-103">ASP.NET Core Blazor plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="8b413-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="8b413-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8b413-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="8b413-105">Requisitos de navegador</span><span class="sxs-lookup"><span data-stu-id="8b413-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="8b413-106">Navegador</span><span class="sxs-lookup"><span data-stu-id="8b413-106">Browser</span></span>                          | <span data-ttu-id="8b413-107">Versão</span><span class="sxs-lookup"><span data-stu-id="8b413-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="8b413-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="8b413-108">Microsoft Edge</span></span>                   | <span data-ttu-id="8b413-109">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-109">Current</span></span>               |
| <span data-ttu-id="8b413-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="8b413-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="8b413-111">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-111">Current</span></span>               |
| <span data-ttu-id="8b413-112">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="8b413-112">Google Chrome, including Android</span></span> | <span data-ttu-id="8b413-113">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-113">Current</span></span>               |
| <span data-ttu-id="8b413-114">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="8b413-114">Safari, including iOS</span></span>            | <span data-ttu-id="8b413-115">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-115">Current</span></span>               |
| <span data-ttu-id="8b413-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8b413-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="8b413-117">Sem suporte&dagger;</span><span class="sxs-lookup"><span data-stu-id="8b413-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="8b413-118">&dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="8b413-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="8b413-119">Navegador</span><span class="sxs-lookup"><span data-stu-id="8b413-119">Browser</span></span>                          | <span data-ttu-id="8b413-120">Versão</span><span class="sxs-lookup"><span data-stu-id="8b413-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="8b413-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="8b413-121">Microsoft Edge</span></span>                   | <span data-ttu-id="8b413-122">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-122">Current</span></span>    |
| <span data-ttu-id="8b413-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="8b413-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="8b413-124">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-124">Current</span></span>    |
| <span data-ttu-id="8b413-125">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="8b413-125">Google Chrome, including Android</span></span> | <span data-ttu-id="8b413-126">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-126">Current</span></span>    |
| <span data-ttu-id="8b413-127">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="8b413-127">Safari, including iOS</span></span>            | <span data-ttu-id="8b413-128">Current</span><span class="sxs-lookup"><span data-stu-id="8b413-128">Current</span></span>    |
| <span data-ttu-id="8b413-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8b413-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="8b413-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="8b413-130">11&dagger;</span></span> |

<span data-ttu-id="8b413-131">&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote).</span><span class="sxs-lookup"><span data-stu-id="8b413-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b413-132">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8b413-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
