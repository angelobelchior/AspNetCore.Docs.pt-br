---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630738"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="f86d5-103">Exemplos de autenticação para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f86d5-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="f86d5-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f86d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f86d5-105">O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="f86d5-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f86d5-106">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="f86d5-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="f86d5-107">[Cookie Authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="f86d5-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="f86d5-108">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f86d5-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f86d5-109">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="f86d5-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="f86d5-110">[Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="f86d5-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="f86d5-111">Seleção entre cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="f86d5-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f86d5-112">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="f86d5-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f86d5-113">Executar as amostras</span><span class="sxs-lookup"><span data-stu-id="f86d5-113">Run the samples</span></span>

* <span data-ttu-id="f86d5-114">Selecione uma [ramificação](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f86d5-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="f86d5-115">Por exemplo, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="f86d5-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="f86d5-116">Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f86d5-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="f86d5-117">Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f86d5-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f86d5-118">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="f86d5-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f86d5-119">O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="f86d5-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f86d5-120">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="f86d5-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="f86d5-121">[Cookie Authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="f86d5-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="f86d5-122">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f86d5-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f86d5-123">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="f86d5-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="f86d5-124">[Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="f86d5-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="f86d5-125">Seleção entre cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="f86d5-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f86d5-126">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="f86d5-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f86d5-127">Executar as amostras</span><span class="sxs-lookup"><span data-stu-id="f86d5-127">Run the samples</span></span>

* <span data-ttu-id="f86d5-128">Selecione uma [ramificação](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f86d5-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="f86d5-129">Por exemplo, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="f86d5-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="f86d5-130">Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f86d5-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="f86d5-131">Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f86d5-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f86d5-132">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="f86d5-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
