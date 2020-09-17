---
title: SDK do ASP.NET Core Web
author: Rick-Anderson
description: Visão geral de Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: fdc2991060050c813efb6dc895cf73ff876bae29
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722495"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="11141-103">SDK do ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="11141-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="11141-104">Visão geral</span><span class="sxs-lookup"><span data-stu-id="11141-104">Overview</span></span>

<span data-ttu-id="11141-105">`Microsoft.NET.Sdk.Web` é um [SDK de projeto do MSBuild](/visualstudio/msbuild/how-to-use-project-sdk) para compilar ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="11141-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="11141-106">É possível criar um aplicativo ASP.NET Core sem esse SDK, no entanto, o SDK da Web é:</span><span class="sxs-lookup"><span data-stu-id="11141-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="11141-107">Adaptado em direção à oferta de uma experiência de primeira classe.</span><span class="sxs-lookup"><span data-stu-id="11141-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="11141-108">O destino recomendado para a maioria dos usuários.</span><span class="sxs-lookup"><span data-stu-id="11141-108">The recommended target for most users.</span></span>

<span data-ttu-id="11141-109">Use o Web. SDK em um projeto do:</span><span class="sxs-lookup"><span data-stu-id="11141-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="11141-110">Recursos habilitados usando o SDK da Web:</span><span class="sxs-lookup"><span data-stu-id="11141-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="11141-111">Projetos voltados para o .NET Core 3,0 ou posterior referência implícita:</span><span class="sxs-lookup"><span data-stu-id="11141-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="11141-112">A [estrutura compartilhada ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="11141-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="11141-113">[Analisadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) projetados para a criação de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11141-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="11141-114">O SDK da Web importa destinos do MSBuild que habilitam o uso de perfis de publicação e publicação usando o WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="11141-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="11141-115">Propriedades</span><span class="sxs-lookup"><span data-stu-id="11141-115">Properties</span></span>

| <span data-ttu-id="11141-116">Propriedade</span><span class="sxs-lookup"><span data-stu-id="11141-116">Property</span></span> | <span data-ttu-id="11141-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="11141-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="11141-118">Desabilita a referência implícita à `Microsoft.AspNetCore.App` estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="11141-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="11141-119">Desabilita a referência implícita a ASP.NET Core analisadores.</span><span class="sxs-lookup"><span data-stu-id="11141-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="11141-120">Desabilita a referência implícita aos Razor analisadores de componentes durante a Blazor compilação de aplicativos (servidor).</span><span class="sxs-lookup"><span data-stu-id="11141-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |