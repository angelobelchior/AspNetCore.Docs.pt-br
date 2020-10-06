---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754535"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="98215-103">ASP.NET Core Blazor plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="98215-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="98215-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98215-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="98215-105">Blazor WebAssembly e Blazor Server têm suporte nos navegadores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="98215-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="98215-106">Navegador</span><span class="sxs-lookup"><span data-stu-id="98215-106">Browser</span></span>                          | <span data-ttu-id="98215-107">Versão</span><span class="sxs-lookup"><span data-stu-id="98215-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="98215-108">Apple Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="98215-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="98215-109">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-109">Current&dagger;</span></span> |
| <span data-ttu-id="98215-110">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="98215-110">Google Chrome, including Android</span></span> | <span data-ttu-id="98215-111">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-111">Current&dagger;</span></span> |
| <span data-ttu-id="98215-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="98215-112">Microsoft Edge</span></span>                   | <span data-ttu-id="98215-113">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-113">Current&dagger;</span></span> |
| <span data-ttu-id="98215-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="98215-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="98215-115">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-115">Current&dagger;</span></span> |  

<span data-ttu-id="98215-116">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="98215-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="98215-117">Navegador</span><span class="sxs-lookup"><span data-stu-id="98215-117">Browser</span></span>                          | <span data-ttu-id="98215-118">Versão</span><span class="sxs-lookup"><span data-stu-id="98215-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="98215-119">Apple Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="98215-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="98215-120">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-120">Current&dagger;</span></span>       |
| <span data-ttu-id="98215-121">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="98215-121">Google Chrome, including Android</span></span> | <span data-ttu-id="98215-122">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-122">Current&dagger;</span></span>       |
| <span data-ttu-id="98215-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="98215-123">Microsoft Edge</span></span>                   | <span data-ttu-id="98215-124">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-124">Current&dagger;</span></span>       |
| <span data-ttu-id="98215-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="98215-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="98215-126">Sem suporte&Dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="98215-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="98215-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="98215-128">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-128">Current&dagger;</span></span>       |  

<span data-ttu-id="98215-129">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="98215-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="98215-130">&Dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="98215-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="98215-131">Navegador</span><span class="sxs-lookup"><span data-stu-id="98215-131">Browser</span></span>                          | <span data-ttu-id="98215-132">Versão</span><span class="sxs-lookup"><span data-stu-id="98215-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="98215-133">Apple Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="98215-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="98215-134">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-134">Current&dagger;</span></span> |
| <span data-ttu-id="98215-135">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="98215-135">Google Chrome, including Android</span></span> | <span data-ttu-id="98215-136">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-136">Current&dagger;</span></span> |
| <span data-ttu-id="98215-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="98215-137">Microsoft Edge</span></span>                   | <span data-ttu-id="98215-138">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-138">Current&dagger;</span></span> |
| <span data-ttu-id="98215-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="98215-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="98215-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-140">11&Dagger;</span></span>      |
| <span data-ttu-id="98215-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="98215-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="98215-142">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="98215-142">Current&dagger;</span></span> |

<span data-ttu-id="98215-143">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="98215-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="98215-144">&Dagger;Os suportes retroativos adicionais são necessários.</span><span class="sxs-lookup"><span data-stu-id="98215-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="98215-145">Por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote.</span><span class="sxs-lookup"><span data-stu-id="98215-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="98215-146">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="98215-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
