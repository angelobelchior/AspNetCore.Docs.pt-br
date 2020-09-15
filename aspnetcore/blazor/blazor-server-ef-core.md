---
title: ASP.NET Core Blazor Server com Entity Framework Core (EFCore)
author: JeremyLikness
description: Diretrizes para usar EF Core em Blazor Server aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: e548465b3d79279802fbfacd66c69724d864d14d
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080323"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="19cc7-103">ASP.NET Core Blazor Server com Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="19cc7-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="19cc7-104">Por: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="19cc7-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="19cc7-105">Blazor Server é uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="19cc7-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="19cc7-106">O aplicativo mantém uma conexão contínua com o servidor e o estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="19cc7-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="19cc7-107">Um exemplo de estado do usuário são os dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="19cc7-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="19cc7-108">O modelo de aplicativo exclusivo que Blazor Server o fornece requer uma abordagem especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="19cc7-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="19cc7-109">Este artigo aborda EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="19cc7-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="19cc7-110">Blazor WebAssembly os aplicativos são executados em uma área restrita do Webassembly que impede a maioria das conexões de banco de dados direta.</span><span class="sxs-lookup"><span data-stu-id="19cc7-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="19cc7-111">A execução do EF Core no Blazor WebAssembly está além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="19cc7-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="19cc7-112">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="19cc7-112">Sample app</span></span></h2>

<span data-ttu-id="19cc7-113">O aplicativo de exemplo foi criado como uma referência para Blazor Server aplicativos que usam EF Core.</span><span class="sxs-lookup"><span data-stu-id="19cc7-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="19cc7-114">O aplicativo de exemplo inclui uma grade com operações de classificação e filtragem, exclusão, adição e atualização.</span><span class="sxs-lookup"><span data-stu-id="19cc7-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="19cc7-115">O exemplo demonstra o uso de EF Core para lidar com a simultaneidade otimista.</span><span class="sxs-lookup"><span data-stu-id="19cc7-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="19cc7-116">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="19cc7-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="19cc7-117">O exemplo usa um banco de dados [SQLite](https://www.sqlite.org/index.html) local para que ele possa ser usado em qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="19cc7-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="19cc7-118">O exemplo também configura o log do banco de dados para mostrar as consultas SQL que são geradas.</span><span class="sxs-lookup"><span data-stu-id="19cc7-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="19cc7-119">Isso é configurado em `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="19cc7-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="19cc7-120">Os componentes grade, adicionar e exibir usam o padrão "contexto por operação", em que um contexto é criado para cada operação.</span><span class="sxs-lookup"><span data-stu-id="19cc7-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="19cc7-121">O componente de edição usa o padrão "contexto por componente", em que um contexto é criado para cada componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="19cc7-122">Alguns dos exemplos de código neste tópico exigem namespaces e serviços que não são mostrados.</span><span class="sxs-lookup"><span data-stu-id="19cc7-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="19cc7-123">Para inspecionar o código totalmente funcional, incluindo as necessárias [`@using`](xref:mvc/views/razor#using) e as [`@inject`](xref:mvc/views/razor#inject) diretivas para obter Razor exemplos, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="19cc7-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="19cc7-124">Acesso ao banco de dados</span><span class="sxs-lookup"><span data-stu-id="19cc7-124">Database access</span></span></h2>

<span data-ttu-id="19cc7-125">EF Core se baseia em um <xref:Microsoft.EntityFrameworkCore.DbContext> como o meio de [Configurar o acesso ao banco de dados](/ef/core/miscellaneous/configuring-dbcontext) e agir como uma [*unidade de trabalho*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="19cc7-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="19cc7-126">EF Core fornece a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extensão para ASP.NET Core aplicativos que registram o contexto como um serviço com *escopo* por padrão.</span><span class="sxs-lookup"><span data-stu-id="19cc7-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="19cc7-127">Em Blazor Server aplicativos, os registros de serviço com escopo podem ser problemáticos porque a instância é compartilhada entre os componentes dentro do circuito do usuário.</span><span class="sxs-lookup"><span data-stu-id="19cc7-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="19cc7-128"><xref:Microsoft.EntityFrameworkCore.DbContext> Não é thread-safe e não é projetado para uso simultâneo.</span><span class="sxs-lookup"><span data-stu-id="19cc7-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="19cc7-129">Os tempos de vida existentes são inadequados por esses motivos:</span><span class="sxs-lookup"><span data-stu-id="19cc7-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="19cc7-130">**Singleton** compartilha o estado entre todos os usuários do aplicativo e leva a uso simultâneo inadequado.</span><span class="sxs-lookup"><span data-stu-id="19cc7-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="19cc7-131">Com **escopo** (o padrão) representa um problema semelhante entre os componentes do mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="19cc7-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="19cc7-132">Os resultados **transitórios** em uma nova instância por solicitação; Mas como os componentes podem ser de longa duração, isso resulta em um contexto de vida maior do que pode ser pretendido.</span><span class="sxs-lookup"><span data-stu-id="19cc7-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="19cc7-133">As recomendações a seguir foram projetadas para fornecer uma abordagem consistente ao uso de EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="19cc7-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="19cc7-134">Por padrão, considere usar um contexto por operação.</span><span class="sxs-lookup"><span data-stu-id="19cc7-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="19cc7-135">O contexto é projetado para uma instanciação rápida e baixa de sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="19cc7-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="19cc7-136">Use um sinalizador para evitar várias operações simultâneas:</span><span class="sxs-lookup"><span data-stu-id="19cc7-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="19cc7-137">Coloque as operações após a `Loading = true;` linha no `try` bloco.</span><span class="sxs-lookup"><span data-stu-id="19cc7-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="19cc7-138">Para operações de vida mais longa que aproveitam o [controle de simultaneidade](/ef/core/saving/concurrency), [controle de alterações](/ef/core/querying/tracking) do EF Core, faça [o escopo do contexto para o tempo de vida do componente](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="19cc7-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="19cc7-139">Novas instâncias de DbContext</span><span class="sxs-lookup"><span data-stu-id="19cc7-139">New DbContext instances</span></span></h3>

<span data-ttu-id="19cc7-140">A maneira mais rápida de criar uma nova <xref:Microsoft.EntityFrameworkCore.DbContext> instância é usando `new` o para criar uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="19cc7-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="19cc7-141">No entanto, há vários cenários que podem exigir a resolução de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="19cc7-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="19cc7-142">Por exemplo, você pode desejar usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar o contexto.</span><span class="sxs-lookup"><span data-stu-id="19cc7-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="19cc7-143">A solução recomendada para criar um novo <xref:Microsoft.EntityFrameworkCore.DbContext> com dependências é usar uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="19cc7-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="19cc7-144">O EF Core 5,0 ou posterior fornece uma fábrica interna para a criação de novos contextos.</span><span class="sxs-lookup"><span data-stu-id="19cc7-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="19cc7-145">O exemplo a seguir configura o [SQLite](https://www.sqlite.org/index.html) e habilita o log de dados.</span><span class="sxs-lookup"><span data-stu-id="19cc7-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="19cc7-146">O código usa um método de extensão para configurar a fábrica de banco de dados para DI e fornecer opções padrão:</span><span class="sxs-lookup"><span data-stu-id="19cc7-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="19cc7-147">A fábrica é injetada em componentes e usada para criar novas instâncias.</span><span class="sxs-lookup"><span data-stu-id="19cc7-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="19cc7-148">Por exemplo, em `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="19cc7-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="19cc7-149">`Wrapper` é uma [referência de componente](xref:blazor/components/index#capture-references-to-components) para o `GridWrapper` componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="19cc7-150">Consulte o `Index` componente ( `Pages/Index.razor` ) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="19cc7-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="19cc7-151">Escopo para o tempo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="19cc7-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="19cc7-152">Talvez você queira criar um <xref:Microsoft.EntityFrameworkCore.DbContext> que existe durante o tempo de vida de um componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="19cc7-153">Isso permite que você o use como uma [unidade de trabalho](https://martinfowler.com/eaaCatalog/unitOfWork.html) e aproveite os recursos internos, como o controle de alterações e a resolução de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="19cc7-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="19cc7-154">Você pode usar a fábrica para criar um contexto e rastreá-lo durante o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="19cc7-155">Primeiro, implemente <xref:System.IDisposable> e insira a fábrica conforme mostrado em `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="19cc7-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="19cc7-156">O aplicativo de exemplo garante que o contexto seja Descartado quando o componente for descartado:</span><span class="sxs-lookup"><span data-stu-id="19cc7-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="19cc7-157">Por fim, [`OnInitializedAsync`](xref:blazor/components/lifecycle) é substituído para criar um novo contexto.</span><span class="sxs-lookup"><span data-stu-id="19cc7-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="19cc7-158">No aplicativo de exemplo, o [`OnInitializedAsync`](xref:blazor/components/lifecycle) carrega o contato no mesmo método:</span><span class="sxs-lookup"><span data-stu-id="19cc7-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="19cc7-159">Blazor Server é uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="19cc7-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="19cc7-160">O aplicativo mantém uma conexão contínua com o servidor e o estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="19cc7-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="19cc7-161">Um exemplo de estado do usuário são os dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="19cc7-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="19cc7-162">O modelo de aplicativo exclusivo que Blazor Server o fornece requer uma abordagem especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="19cc7-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="19cc7-163">Este artigo aborda EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="19cc7-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="19cc7-164">Blazor WebAssembly os aplicativos são executados em uma área restrita do Webassembly que impede a maioria das conexões de banco de dados direta.</span><span class="sxs-lookup"><span data-stu-id="19cc7-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="19cc7-165">A execução do EF Core no Blazor WebAssembly está além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="19cc7-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="19cc7-166">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="19cc7-166">Sample app</span></span></h2>

<span data-ttu-id="19cc7-167">O aplicativo de exemplo foi criado como uma referência para Blazor Server aplicativos que usam EF Core.</span><span class="sxs-lookup"><span data-stu-id="19cc7-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="19cc7-168">O aplicativo de exemplo inclui uma grade com operações de classificação e filtragem, exclusão, adição e atualização.</span><span class="sxs-lookup"><span data-stu-id="19cc7-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="19cc7-169">O exemplo demonstra o uso de EF Core para lidar com a simultaneidade otimista.</span><span class="sxs-lookup"><span data-stu-id="19cc7-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="19cc7-170">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="19cc7-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="19cc7-171">O exemplo usa um banco de dados [SQLite](https://www.sqlite.org/index.html) local para que ele possa ser usado em qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="19cc7-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="19cc7-172">O exemplo também configura o log do banco de dados para mostrar as consultas SQL que são geradas.</span><span class="sxs-lookup"><span data-stu-id="19cc7-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="19cc7-173">Isso é configurado em `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="19cc7-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="19cc7-174">Os componentes grade, adicionar e exibir usam o padrão "contexto por operação", em que um contexto é criado para cada operação.</span><span class="sxs-lookup"><span data-stu-id="19cc7-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="19cc7-175">O componente de edição usa o padrão "contexto por componente", em que um contexto é criado para cada componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="19cc7-176">Alguns dos exemplos de código neste tópico exigem namespaces e serviços que não são mostrados.</span><span class="sxs-lookup"><span data-stu-id="19cc7-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="19cc7-177">Para inspecionar o código totalmente funcional, incluindo as necessárias [`@using`](xref:mvc/views/razor#using) e as [`@inject`](xref:mvc/views/razor#inject) diretivas para obter Razor exemplos, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="19cc7-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="19cc7-178">Acesso ao banco de dados</span><span class="sxs-lookup"><span data-stu-id="19cc7-178">Database access</span></span></h2>

<span data-ttu-id="19cc7-179">EF Core se baseia em um <xref:Microsoft.EntityFrameworkCore.DbContext> como o meio de [Configurar o acesso ao banco de dados](/ef/core/miscellaneous/configuring-dbcontext) e agir como uma [*unidade de trabalho*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="19cc7-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="19cc7-180">EF Core fornece a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extensão para ASP.NET Core aplicativos que registram o contexto como um serviço com *escopo* por padrão.</span><span class="sxs-lookup"><span data-stu-id="19cc7-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="19cc7-181">Em Blazor Server aplicativos, isso pode ser problemático porque a instância é compartilhada entre componentes dentro do circuito do usuário.</span><span class="sxs-lookup"><span data-stu-id="19cc7-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="19cc7-182"><xref:Microsoft.EntityFrameworkCore.DbContext> Não é thread-safe e não é projetado para uso simultâneo.</span><span class="sxs-lookup"><span data-stu-id="19cc7-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="19cc7-183">Os tempos de vida existentes são inadequados por esses motivos:</span><span class="sxs-lookup"><span data-stu-id="19cc7-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="19cc7-184">**Singleton** compartilha o estado entre todos os usuários do aplicativo e leva a uso simultâneo inadequado.</span><span class="sxs-lookup"><span data-stu-id="19cc7-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="19cc7-185">Com **escopo** (o padrão) representa um problema semelhante entre os componentes do mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="19cc7-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="19cc7-186">Os resultados **transitórios** em uma nova instância por solicitação; Mas como os componentes podem ser de longa duração, isso resulta em um contexto de vida maior do que pode ser pretendido.</span><span class="sxs-lookup"><span data-stu-id="19cc7-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="19cc7-187">As recomendações a seguir foram projetadas para fornecer uma abordagem consistente ao uso de EF Core em Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="19cc7-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="19cc7-188">Por padrão, considere usar um contexto por operação.</span><span class="sxs-lookup"><span data-stu-id="19cc7-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="19cc7-189">O contexto é projetado para uma instanciação rápida e baixa de sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="19cc7-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="19cc7-190">Use um sinalizador para evitar várias operações simultâneas:</span><span class="sxs-lookup"><span data-stu-id="19cc7-190">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="19cc7-191">Coloque as operações após a `Loading = true;` linha no `try` bloco.</span><span class="sxs-lookup"><span data-stu-id="19cc7-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="19cc7-192">Para operações de vida mais longa que aproveitam o [controle de simultaneidade](/ef/core/saving/concurrency), [controle de alterações](/ef/core/querying/tracking) do EF Core, faça [o escopo do contexto para o tempo de vida do componente](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="19cc7-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="19cc7-193">Novas instâncias de DbContext</span><span class="sxs-lookup"><span data-stu-id="19cc7-193">New DbContext instances</span></span></h3>

<span data-ttu-id="19cc7-194">A maneira mais rápida de criar uma nova <xref:Microsoft.EntityFrameworkCore.DbContext> instância é usando `new` o para criar uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="19cc7-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="19cc7-195">No entanto, há vários cenários que podem exigir a resolução de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="19cc7-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="19cc7-196">Por exemplo, você pode desejar usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar o contexto.</span><span class="sxs-lookup"><span data-stu-id="19cc7-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="19cc7-197">A solução recomendada para criar um novo <xref:Microsoft.EntityFrameworkCore.DbContext> com dependências é usar uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="19cc7-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="19cc7-198">O aplicativo de exemplo implementa sua própria fábrica no `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="19cc7-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="19cc7-199">Na fábrica anterior, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfaz qualquer dependência por meio do provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="19cc7-199">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="19cc7-200">O exemplo a seguir configura o [SQLite](https://www.sqlite.org/index.html) e habilita o log de dados.</span><span class="sxs-lookup"><span data-stu-id="19cc7-200">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="19cc7-201">O código usa um método de extensão para configurar a fábrica de banco de dados para DI e fornecer opções padrão:</span><span class="sxs-lookup"><span data-stu-id="19cc7-201">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="19cc7-202">A fábrica é injetada em componentes e usada para criar novas instâncias.</span><span class="sxs-lookup"><span data-stu-id="19cc7-202">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="19cc7-203">Por exemplo, em `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="19cc7-203">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="19cc7-204">`Wrapper` é uma [referência de componente](xref:blazor/components/index#capture-references-to-components) para o `GridWrapper` componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-204">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="19cc7-205">Consulte o `Index` componente ( `Pages/Index.razor` ) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="19cc7-205">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="19cc7-206">Escopo para o tempo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="19cc7-206">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="19cc7-207">Talvez você queira criar um <xref:Microsoft.EntityFrameworkCore.DbContext> que existe durante o tempo de vida de um componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-207">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="19cc7-208">Isso permite que você o use como uma [unidade de trabalho](https://martinfowler.com/eaaCatalog/unitOfWork.html) e aproveite os recursos internos, como o controle de alterações e a resolução de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="19cc7-208">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="19cc7-209">Você pode usar a fábrica para criar um contexto e rastreá-lo durante o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="19cc7-209">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="19cc7-210">Primeiro, implemente <xref:System.IDisposable> e insira a fábrica conforme mostrado em `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="19cc7-210">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="19cc7-211">O aplicativo de exemplo garante que o contexto seja Descartado quando o componente for descartado:</span><span class="sxs-lookup"><span data-stu-id="19cc7-211">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="19cc7-212">Por fim, [`OnInitializedAsync`](xref:blazor/components/lifecycle) é substituído para criar um novo contexto.</span><span class="sxs-lookup"><span data-stu-id="19cc7-212">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="19cc7-213">No aplicativo de exemplo, o [`OnInitializedAsync`](xref:blazor/components/lifecycle) carrega o contato no mesmo método:</span><span class="sxs-lookup"><span data-stu-id="19cc7-213">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="19cc7-214">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="19cc7-214">In the preceding example:</span></span>

* <span data-ttu-id="19cc7-215">Quando `Busy` é definido como `true` , as operações assíncronas podem começar.</span><span class="sxs-lookup"><span data-stu-id="19cc7-215">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="19cc7-216">Quando `Busy` é definido de volta como `false` , as operações assíncronas devem ser concluídas.</span><span class="sxs-lookup"><span data-stu-id="19cc7-216">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="19cc7-217">Coloque a lógica de tratamento de erro adicional em um `catch` bloco.</span><span class="sxs-lookup"><span data-stu-id="19cc7-217">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="19cc7-218">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="19cc7-218">Additional resources</span></span>

* [<span data-ttu-id="19cc7-219">Documentação do EF Core</span><span class="sxs-lookup"><span data-stu-id="19cc7-219">EF Core documentation</span></span>](/ef/)
