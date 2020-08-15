---
title: ASP.NET Core Blazor Server com Entity Framework Core (EFCore)
author: JeremyLikness
description: Diretrizes para usar EF Core em Blazor Server aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 2ea702aa73a2981afc223e5c1700d6ec2dc62df4
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227732"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="ba92f-103">ASP.NET Core Blazor Server com Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="ba92f-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="ba92f-104">Por: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="ba92f-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ba92f-105">Blazor Server é uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="ba92f-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="ba92f-106">O aplicativo mantém uma conexão contínua com o servidor e o estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="ba92f-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="ba92f-107">Um exemplo de estado do usuário são os dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="ba92f-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="ba92f-108">O modelo de aplicativo exclusivo que Blazor Server o fornece requer uma abordagem especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="ba92f-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="ba92f-109">Este artigo aborda EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ba92f-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="ba92f-110">Blazor WebAssembly os aplicativos são executados em uma área restrita do Webassembly que impede a maioria das conexões de banco de dados direta.</span><span class="sxs-lookup"><span data-stu-id="ba92f-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="ba92f-111">A execução do EF Core no Blazor WebAssembly está além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="ba92f-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="ba92f-112">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="ba92f-112">Sample app</span></span>

<span data-ttu-id="ba92f-113">O aplicativo de exemplo foi criado como uma referência para Blazor Server aplicativos que usam EF Core.</span><span class="sxs-lookup"><span data-stu-id="ba92f-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="ba92f-114">O aplicativo de exemplo inclui uma grade com operações de classificação e filtragem, exclusão, adição e atualização.</span><span class="sxs-lookup"><span data-stu-id="ba92f-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="ba92f-115">O exemplo demonstra o uso de EF Core para lidar com a simultaneidade otimista.</span><span class="sxs-lookup"><span data-stu-id="ba92f-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="ba92f-116">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba92f-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ba92f-117">O exemplo usa um banco de dados [SQLite](https://www.sqlite.org/index.html) local para que ele possa ser usado em qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="ba92f-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="ba92f-118">O exemplo também configura o log do banco de dados para mostrar as consultas SQL que são geradas.</span><span class="sxs-lookup"><span data-stu-id="ba92f-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="ba92f-119">Isso é configurado em `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="ba92f-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="ba92f-120">Os componentes grade, adicionar e exibir usam o padrão "contexto por operação", em que um contexto é criado para cada operação.</span><span class="sxs-lookup"><span data-stu-id="ba92f-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="ba92f-121">O componente de edição usa o padrão "contexto por componente", em que um contexto é criado para cada componente.</span><span class="sxs-lookup"><span data-stu-id="ba92f-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="ba92f-122">Acesso ao banco de dados</span><span class="sxs-lookup"><span data-stu-id="ba92f-122">Database access</span></span>

<span data-ttu-id="ba92f-123">EF Core se baseia em um <xref:Microsoft.EntityFrameworkCore.DbContext> como o meio de [Configurar o acesso ao banco de dados](/ef/core/miscellaneous/configuring-dbcontext) e agir como uma [_unidade de trabalho_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="ba92f-123">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="ba92f-124">EF Core fornece a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extensão para ASP.NET Core aplicativos que registram o contexto como um serviço com _escopo_ por padrão.</span><span class="sxs-lookup"><span data-stu-id="ba92f-124">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="ba92f-125">Em Blazor Server aplicativos, os registros de serviço com escopo podem ser problemáticos porque a instância é compartilhada entre os componentes dentro do circuito do usuário.</span><span class="sxs-lookup"><span data-stu-id="ba92f-125">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="ba92f-126"><xref:Microsoft.EntityFrameworkCore.DbContext> Não é thread-safe e não é projetado para uso simultâneo.</span><span class="sxs-lookup"><span data-stu-id="ba92f-126"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="ba92f-127">Os tempos de vida existentes são inadequados por esses motivos:</span><span class="sxs-lookup"><span data-stu-id="ba92f-127">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="ba92f-128">**Singleton** compartilha o estado entre todos os usuários do aplicativo e leva a uso simultâneo inadequado.</span><span class="sxs-lookup"><span data-stu-id="ba92f-128">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="ba92f-129">Com **escopo** (o padrão) representa um problema semelhante entre os componentes do mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="ba92f-129">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="ba92f-130">Os resultados **transitórios** em uma nova instância por solicitação; Mas como os componentes podem ser de longa duração, isso resulta em um contexto de vida maior do que pode ser pretendido.</span><span class="sxs-lookup"><span data-stu-id="ba92f-130">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="ba92f-131">As recomendações a seguir foram projetadas para fornecer uma abordagem consistente ao uso de EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ba92f-131">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="ba92f-132">Por padrão, considere usar um contexto por operação.</span><span class="sxs-lookup"><span data-stu-id="ba92f-132">By default, consider using one context per operation.</span></span> <span data-ttu-id="ba92f-133">O contexto é projetado para uma instanciação rápida e baixa de sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="ba92f-133">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="ba92f-134">Use um sinalizador para evitar várias operações simultâneas:</span><span class="sxs-lookup"><span data-stu-id="ba92f-134">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* <span data-ttu-id="ba92f-135">Para operações de vida mais longa que aproveitam o [controle de simultaneidade](/ef/core/saving/concurrency), [controle de alterações](/ef/core/querying/tracking) do EF Core, faça [o escopo do contexto para o tempo de vida do componente](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="ba92f-135">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="ba92f-136">Novas instâncias de DbContext</span><span class="sxs-lookup"><span data-stu-id="ba92f-136">New DbContext instances</span></span>

<span data-ttu-id="ba92f-137">A maneira mais rápida de criar uma nova <xref:Microsoft.EntityFrameworkCore.DbContext> instância é usando `new` o para criar uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="ba92f-137">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="ba92f-138">No entanto, há vários cenários que podem exigir a resolução de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="ba92f-138">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="ba92f-139">Por exemplo, você pode desejar usar [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar o contexto.</span><span class="sxs-lookup"><span data-stu-id="ba92f-139">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="ba92f-140">A solução recomendada para criar um novo <xref:Microsoft.EntityFrameworkCore.DbContext> com dependências é usar uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="ba92f-140">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="ba92f-141">O EF Core 5,0 ou posterior fornece uma fábrica interna para a criação de novos contextos.</span><span class="sxs-lookup"><span data-stu-id="ba92f-141">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="ba92f-142">O exemplo a seguir configura o [SQLite](https://www.sqlite.org/index.html) e habilita o log de dados.</span><span class="sxs-lookup"><span data-stu-id="ba92f-142">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="ba92f-143">O código usa um método de extensão para configurar a fábrica de banco de dados para DI e fornecer opções padrão:</span><span class="sxs-lookup"><span data-stu-id="ba92f-143">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="ba92f-144">A fábrica é injetada em componentes e usada para criar novas instâncias.</span><span class="sxs-lookup"><span data-stu-id="ba92f-144">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="ba92f-145">Por exemplo, em `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="ba92f-145">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="ba92f-146">Escopo para o tempo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="ba92f-146">Scope to the component lifetime</span></span>

<span data-ttu-id="ba92f-147">Talvez você queira criar um <xref:Microsoft.EntityFrameworkCore.DbContext> que existe durante o tempo de vida de um componente.</span><span class="sxs-lookup"><span data-stu-id="ba92f-147">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="ba92f-148">Isso permite que você o use como uma [unidade de trabalho](https://martinfowler.com/eaaCatalog/unitOfWork.html) e aproveite os recursos internos, como o controle de alterações e a resolução de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="ba92f-148">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="ba92f-149">Você pode usar a fábrica para criar um contexto e rastreá-lo durante o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="ba92f-149">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="ba92f-150">Primeiro, implemente <xref:System.IDisposable> e insira a fábrica conforme mostrado em `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="ba92f-150">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="ba92f-151">O aplicativo de exemplo garante que o contato seja Descartado quando o componente for descartado:</span><span class="sxs-lookup"><span data-stu-id="ba92f-151">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="ba92f-152">Por fim, `OnInitializedAsync` é substituído para criar um novo contexto.</span><span class="sxs-lookup"><span data-stu-id="ba92f-152">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="ba92f-153">No aplicativo de exemplo, o `OnInitializedAsync` carrega o contato no mesmo método:</span><span class="sxs-lookup"><span data-stu-id="ba92f-153">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="ba92f-154">Blazor Server é uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="ba92f-154">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="ba92f-155">O aplicativo mantém uma conexão contínua com o servidor e o estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="ba92f-155">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="ba92f-156">Um exemplo de estado do usuário são os dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="ba92f-156">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="ba92f-157">O modelo de aplicativo exclusivo que Blazor Server o fornece requer uma abordagem especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="ba92f-157">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="ba92f-158">Este artigo aborda EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ba92f-158">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="ba92f-159">Blazor WebAssembly os aplicativos são executados em uma área restrita do Webassembly que impede a maioria das conexões de banco de dados direta.</span><span class="sxs-lookup"><span data-stu-id="ba92f-159">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="ba92f-160">A execução do EF Core no Blazor WebAssembly está além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="ba92f-160">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="ba92f-161">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="ba92f-161">Sample app</span></span>

<span data-ttu-id="ba92f-162">O aplicativo de exemplo foi criado como uma referência para Blazor Server aplicativos que usam EF Core.</span><span class="sxs-lookup"><span data-stu-id="ba92f-162">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="ba92f-163">O aplicativo de exemplo inclui uma grade com operações de classificação e filtragem, exclusão, adição e atualização.</span><span class="sxs-lookup"><span data-stu-id="ba92f-163">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="ba92f-164">O exemplo demonstra o uso de EF Core para lidar com a simultaneidade otimista.</span><span class="sxs-lookup"><span data-stu-id="ba92f-164">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="ba92f-165">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba92f-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ba92f-166">O exemplo usa um banco de dados [SQLite](https://www.sqlite.org/index.html) local para que ele possa ser usado em qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="ba92f-166">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="ba92f-167">O exemplo também configura o log do banco de dados para mostrar as consultas SQL que são geradas.</span><span class="sxs-lookup"><span data-stu-id="ba92f-167">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="ba92f-168">Isso é configurado em `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="ba92f-168">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="ba92f-169">Os componentes grade, adicionar e exibir usam o padrão "contexto por operação", em que um contexto é criado para cada operação.</span><span class="sxs-lookup"><span data-stu-id="ba92f-169">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="ba92f-170">O componente de edição usa o padrão "contexto por componente", em que um contexto é criado para cada componente.</span><span class="sxs-lookup"><span data-stu-id="ba92f-170">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="ba92f-171">Acesso ao banco de dados</span><span class="sxs-lookup"><span data-stu-id="ba92f-171">Database access</span></span>

<span data-ttu-id="ba92f-172">EF Core se baseia em um <xref:Microsoft.EntityFrameworkCore.DbContext> como o meio de [Configurar o acesso ao banco de dados](/ef/core/miscellaneous/configuring-dbcontext) e agir como uma [_unidade de trabalho_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="ba92f-172">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="ba92f-173">EF Core fornece a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extensão para ASP.NET Core aplicativos que registram o contexto como um serviço com _escopo_ por padrão.</span><span class="sxs-lookup"><span data-stu-id="ba92f-173">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="ba92f-174">Em Blazor Server aplicativos, isso pode ser problemático porque a instância é compartilhada entre componentes dentro do circuito do usuário.</span><span class="sxs-lookup"><span data-stu-id="ba92f-174">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="ba92f-175"><xref:Microsoft.EntityFrameworkCore.DbContext> Não é thread-safe e não é projetado para uso simultâneo.</span><span class="sxs-lookup"><span data-stu-id="ba92f-175"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="ba92f-176">Os tempos de vida existentes são inadequados por esses motivos:</span><span class="sxs-lookup"><span data-stu-id="ba92f-176">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="ba92f-177">**Singleton** compartilha o estado entre todos os usuários do aplicativo e leva a uso simultâneo inadequado.</span><span class="sxs-lookup"><span data-stu-id="ba92f-177">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="ba92f-178">Com **escopo** (o padrão) representa um problema semelhante entre os componentes do mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="ba92f-178">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="ba92f-179">Os resultados **transitórios** em uma nova instância por solicitação; Mas como os componentes podem ser de longa duração, isso resulta em um contexto de vida maior do que pode ser pretendido.</span><span class="sxs-lookup"><span data-stu-id="ba92f-179">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="ba92f-180">Acesso ao banco de dados</span><span class="sxs-lookup"><span data-stu-id="ba92f-180">Database access</span></span>

<span data-ttu-id="ba92f-181">As recomendações a seguir foram projetadas para fornecer uma abordagem consistente ao uso de EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ba92f-181">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="ba92f-182">Por padrão, considere usar um contexto por operação.</span><span class="sxs-lookup"><span data-stu-id="ba92f-182">By default, consider using one context per operation.</span></span> <span data-ttu-id="ba92f-183">O contexto é projetado para uma instanciação rápida e baixa de sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="ba92f-183">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="ba92f-184">Use um sinalizador para evitar várias operações simultâneas:</span><span class="sxs-lookup"><span data-stu-id="ba92f-184">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* <span data-ttu-id="ba92f-185">Para operações de vida mais longa que aproveitam o [controle de simultaneidade](/ef/core/saving/concurrency), [controle de alterações](/ef/core/querying/tracking) do EF Core, faça [o escopo do contexto para o tempo de vida do componente](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="ba92f-185">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="ba92f-186">Criar novas instâncias de DbContext</span><span class="sxs-lookup"><span data-stu-id="ba92f-186">Create new DbContext instances</span></span>

<span data-ttu-id="ba92f-187">A maneira mais rápida de criar uma nova <xref:Microsoft.EntityFrameworkCore.DbContext> instância é usando `new` o para criar uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="ba92f-187">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="ba92f-188">No entanto, há vários cenários que podem exigir a resolução de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="ba92f-188">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="ba92f-189">Por exemplo, você pode desejar usar [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar o contexto.</span><span class="sxs-lookup"><span data-stu-id="ba92f-189">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="ba92f-190">A solução recomendada para criar um novo <xref:Microsoft.EntityFrameworkCore.DbContext> com dependências é usar uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="ba92f-190">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="ba92f-191">O aplicativo de exemplo implementa sua própria fábrica no `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="ba92f-191">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span> 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="ba92f-192">O exemplo a seguir configura o [SQLite](https://www.sqlite.org/index.html) e habilita o log de dados.</span><span class="sxs-lookup"><span data-stu-id="ba92f-192">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="ba92f-193">O código usa um método de extensão para configurar a fábrica de banco de dados para DI e fornecer opções padrão:</span><span class="sxs-lookup"><span data-stu-id="ba92f-193">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="ba92f-194">A fábrica é injetada em componentes e usada para criar novas instâncias.</span><span class="sxs-lookup"><span data-stu-id="ba92f-194">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="ba92f-195">Por exemplo, em `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="ba92f-195">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="ba92f-196">Escopo para o tempo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="ba92f-196">Scope to the component lifetime</span></span>

<span data-ttu-id="ba92f-197">Talvez você queira criar um <xref:Microsoft.EntityFrameworkCore.DbContext> que existe durante o tempo de vida de um componente.</span><span class="sxs-lookup"><span data-stu-id="ba92f-197">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="ba92f-198">Isso permite que você o use como uma [unidade de trabalho](https://martinfowler.com/eaaCatalog/unitOfWork.html) e aproveite os recursos internos, como o controle de alterações e a resolução de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="ba92f-198">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="ba92f-199">Você pode usar a fábrica para criar um contexto e rastreá-lo durante o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="ba92f-199">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="ba92f-200">Primeiro, implemente <xref:System.IDisposable> e insira a fábrica conforme mostrado em `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="ba92f-200">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="ba92f-201">O aplicativo de exemplo garante que o contato seja Descartado quando o componente for descartado:</span><span class="sxs-lookup"><span data-stu-id="ba92f-201">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="ba92f-202">Por fim, `OnInitializedAsync` é substituído para criar um novo contexto.</span><span class="sxs-lookup"><span data-stu-id="ba92f-202">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="ba92f-203">No aplicativo de exemplo, o `OnInitializedAsync` carrega o contato no mesmo método:</span><span class="sxs-lookup"><span data-stu-id="ba92f-203">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ba92f-204">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ba92f-204">Additional resources</span></span>

> [<span data-ttu-id="ba92f-205">Documentação do EF Core</span><span class="sxs-lookup"><span data-stu-id="ba92f-205">EF Core documentation</span></span>](/ef/)
