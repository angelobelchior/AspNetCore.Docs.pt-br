---
title: Ativação de middleware com um contêiner de terceiros no ASP.NET Core
author: rick-anderson
description: Saiba como usar o middleware fortemente tipado com a ativação baseada em alocador e um contêiner de terceiros no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057759"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="3e883-103">Ativação de middleware com um contêiner de terceiros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e883-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e883-104">Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros.</span><span class="sxs-lookup"><span data-stu-id="3e883-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="3e883-105">Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="3e883-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="3e883-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e883-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3e883-107">O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="3e883-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="3e883-108">O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="3e883-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="3e883-109">A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="3e883-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="3e883-110">O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="3e883-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="3e883-111">O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="3e883-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="3e883-112">O uso do Simple Injector não é um endosso.</span><span class="sxs-lookup"><span data-stu-id="3e883-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="3e883-113">As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e883-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="3e883-114">Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="3e883-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="3e883-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="3e883-115">IMiddlewareFactory</span></span>

<span data-ttu-id="3e883-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.</span><span class="sxs-lookup"><span data-stu-id="3e883-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="3e883-117">No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="3e883-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="3e883-118">O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:</span><span class="sxs-lookup"><span data-stu-id="3e883-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="3e883-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="3e883-119">IMiddleware</span></span>

<span data-ttu-id="3e883-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e883-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="3e883-121">Middleware ativado por uma implementação de `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3e883-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="3e883-122">Uma extensão é criada para o middleware ( *Middleware/MiddlewareExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3e883-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="3e883-123">O `Startup.ConfigureServices` precisa executar várias tarefas:</span><span class="sxs-lookup"><span data-stu-id="3e883-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="3e883-124">Configure o contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e883-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="3e883-125">Registre o alocador e o middleware.</span><span class="sxs-lookup"><span data-stu-id="3e883-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="3e883-126">Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e883-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3e883-127">O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3e883-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e883-128">Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros.</span><span class="sxs-lookup"><span data-stu-id="3e883-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="3e883-129">Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="3e883-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="3e883-130">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e883-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3e883-131">O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="3e883-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="3e883-132">O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="3e883-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="3e883-133">A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="3e883-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="3e883-134">O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="3e883-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="3e883-135">O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="3e883-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="3e883-136">O uso do Simple Injector não é um endosso.</span><span class="sxs-lookup"><span data-stu-id="3e883-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="3e883-137">As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e883-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="3e883-138">Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="3e883-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="3e883-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="3e883-139">IMiddlewareFactory</span></span>

<span data-ttu-id="3e883-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.</span><span class="sxs-lookup"><span data-stu-id="3e883-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="3e883-141">No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="3e883-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="3e883-142">O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:</span><span class="sxs-lookup"><span data-stu-id="3e883-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="3e883-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="3e883-143">IMiddleware</span></span>

<span data-ttu-id="3e883-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e883-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="3e883-145">Middleware ativado por uma implementação de `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3e883-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="3e883-146">Uma extensão é criada para o middleware ( *Middleware/MiddlewareExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3e883-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="3e883-147">O `Startup.ConfigureServices` precisa executar várias tarefas:</span><span class="sxs-lookup"><span data-stu-id="3e883-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="3e883-148">Configure o contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e883-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="3e883-149">Registre o alocador e o middleware.</span><span class="sxs-lookup"><span data-stu-id="3e883-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="3e883-150">Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e883-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3e883-151">O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3e883-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3e883-152">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3e883-152">Additional resources</span></span>

* [<span data-ttu-id="3e883-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="3e883-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="3e883-154">Ativação de middleware de fábrica</span><span class="sxs-lookup"><span data-stu-id="3e883-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="3e883-155">Repositório do GitHub do Simple Injector</span><span class="sxs-lookup"><span data-stu-id="3e883-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="3e883-156">Documentação do Simple Injector</span><span class="sxs-lookup"><span data-stu-id="3e883-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
