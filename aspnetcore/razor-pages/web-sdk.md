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
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634807"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="7d063-103">SDK do ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="7d063-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="7d063-104">Visão geral</span><span class="sxs-lookup"><span data-stu-id="7d063-104">Overview</span></span>

<span data-ttu-id="7d063-105">`Microsoft.NET.Sdk.Web` é um [SDK de projeto do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="7d063-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="7d063-106">É possível criar um aplicativo ASP.NET Core sem esse SDK, no entanto, o SDK da Web é:</span><span class="sxs-lookup"><span data-stu-id="7d063-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="7d063-107">Adaptado em direção à oferta de uma experiência de primeira classe.</span><span class="sxs-lookup"><span data-stu-id="7d063-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="7d063-108">O destino recomendado para a maioria dos usuários.</span><span class="sxs-lookup"><span data-stu-id="7d063-108">The recommended target for most users.</span></span>

<span data-ttu-id="7d063-109">Use o Web. SDK em um projeto do:</span><span class="sxs-lookup"><span data-stu-id="7d063-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="7d063-110">Recursos habilitados usando o SDK da Web:</span><span class="sxs-lookup"><span data-stu-id="7d063-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="7d063-111">Projetos voltados para o .NET Core 3,0 ou posterior referência implícita:</span><span class="sxs-lookup"><span data-stu-id="7d063-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="7d063-112">A [estrutura compartilhada ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7d063-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="7d063-113">[Analisadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) projetados para a criação de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7d063-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="7d063-114">O SDK da Web importa destinos do MSBuild que habilitam o uso de perfis de publicação e publicação usando o WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="7d063-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="7d063-115">Propriedades</span><span class="sxs-lookup"><span data-stu-id="7d063-115">Properties</span></span>

| <span data-ttu-id="7d063-116">Propriedade</span><span class="sxs-lookup"><span data-stu-id="7d063-116">Property</span></span> | <span data-ttu-id="7d063-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="7d063-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="7d063-118">Desabilita a referência implícita à `Microsoft.AspNetCore.App` estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="7d063-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="7d063-119">Desabilita a referência implícita a ASP.NET Core analisadores.</span><span class="sxs-lookup"><span data-stu-id="7d063-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="7d063-120">Desabilita a referência implícita aos Razor analisadores de componentes durante a Blazor compilação de aplicativos (servidor).</span><span class="sxs-lookup"><span data-stu-id="7d063-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
