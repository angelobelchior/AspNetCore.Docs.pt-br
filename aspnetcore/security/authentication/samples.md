---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060333"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="cf964-103">Exemplos de autenticação para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf964-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="cf964-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cf964-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf964-105">O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="cf964-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="cf964-106">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="cf964-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="cf964-107">[:::no-loc(Cookie)::: Authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="cf964-107">[:::no-loc(Cookie)::: authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Cookie):::s)</span></span>
* [<span data-ttu-id="cf964-108">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="cf964-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="cf964-109">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="cf964-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="cf964-110">[Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="cf964-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span></span>
* [<span data-ttu-id="cf964-111">Seleção entre :::no-loc(cookie)::: e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="cf964-111">Selecting between :::no-loc(cookie)::: and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="cf964-112">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="cf964-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="cf964-113">Executar as amostras</span><span class="sxs-lookup"><span data-stu-id="cf964-113">Run the samples</span></span>

* <span data-ttu-id="cf964-114">Selecione uma [ramificação](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="cf964-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="cf964-115">Por exemplo, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="cf964-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="cf964-116">Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="cf964-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="cf964-117">Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf964-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="cf964-118">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="cf964-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf964-119">O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="cf964-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="cf964-120">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="cf964-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="cf964-121">[:::no-loc(Cookie)::: Authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="cf964-121">[:::no-loc(Cookie)::: authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Cookie):::s)</span></span>
* [<span data-ttu-id="cf964-122">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="cf964-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="cf964-123">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="cf964-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="cf964-124">[Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="cf964-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span></span>
* [<span data-ttu-id="cf964-125">Seleção entre :::no-loc(cookie)::: e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="cf964-125">Selecting between :::no-loc(cookie)::: and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="cf964-126">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="cf964-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="cf964-127">Executar as amostras</span><span class="sxs-lookup"><span data-stu-id="cf964-127">Run the samples</span></span>

* <span data-ttu-id="cf964-128">Selecione uma [ramificação](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="cf964-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="cf964-129">Por exemplo, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="cf964-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="cf964-130">Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="cf964-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="cf964-131">Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf964-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="cf964-132">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="cf964-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
