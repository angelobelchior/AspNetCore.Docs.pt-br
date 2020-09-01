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
ms.openlocfilehash: 7627d6981fbee66ba19a7065cefb197e50a5fd25
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153513"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server com Entity Framework Core (EFCore)

Por: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server é uma estrutura de aplicativo com estado. O aplicativo mantém uma conexão contínua com o servidor e o estado do usuário é mantido na memória do servidor em um *circuito*. Um exemplo de estado do usuário são os dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito. O modelo de aplicativo exclusivo que Blazor Server o fornece requer uma abordagem especial para usar Entity Framework Core.

> [!NOTE]
> Este artigo aborda EF Core em Blazor Server aplicativos. Blazor WebAssembly os aplicativos são executados em uma área restrita do Webassembly que impede a maioria das conexões de banco de dados direta. A execução do EF Core no Blazor WebAssembly está além do escopo deste artigo.

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo foi criado como uma referência para Blazor Server aplicativos que usam EF Core. O aplicativo de exemplo inclui uma grade com operações de classificação e filtragem, exclusão, adição e atualização. O exemplo demonstra o uso de EF Core para lidar com a simultaneidade otimista.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([como baixar](xref:index#how-to-download-a-sample))

O exemplo usa um banco de dados [SQLite](https://www.sqlite.org/index.html) local para que ele possa ser usado em qualquer plataforma. O exemplo também configura o log do banco de dados para mostrar as consultas SQL que são geradas. Isso é configurado em `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Os componentes grade, adicionar e exibir usam o padrão "contexto por operação", em que um contexto é criado para cada operação. O componente de edição usa o padrão "contexto por componente", em que um contexto é criado para cada componente.

> [!NOTE]
> Alguns dos exemplos de código neste tópico exigem namespaces e serviços que não são mostrados. Para inspecionar o código totalmente funcional, incluindo as necessárias [`@using`](xref:mvc/views/razor#using) e as [`@inject`](xref:mvc/views/razor#inject) diretivas para obter Razor exemplos, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

## <a name="database-access"></a>Acesso ao banco de dados

EF Core se baseia em um <xref:Microsoft.EntityFrameworkCore.DbContext> como o meio de [Configurar o acesso ao banco de dados](/ef/core/miscellaneous/configuring-dbcontext) e agir como uma [*unidade de trabalho*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core fornece a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extensão para ASP.NET Core aplicativos que registram o contexto como um serviço com *escopo* por padrão. Em Blazor Server aplicativos, os registros de serviço com escopo podem ser problemáticos porque a instância é compartilhada entre os componentes dentro do circuito do usuário. <xref:Microsoft.EntityFrameworkCore.DbContext> Não é thread-safe e não é projetado para uso simultâneo. Os tempos de vida existentes são inadequados por esses motivos:

* **Singleton** compartilha o estado entre todos os usuários do aplicativo e leva a uso simultâneo inadequado.
* Com **escopo** (o padrão) representa um problema semelhante entre os componentes do mesmo usuário.
* Os resultados **transitórios** em uma nova instância por solicitação; Mas como os componentes podem ser de longa duração, isso resulta em um contexto de vida maior do que pode ser pretendido.

As recomendações a seguir foram projetadas para fornecer uma abordagem consistente ao uso de EF Core em Blazor Server aplicativos.

* Por padrão, considere usar um contexto por operação. O contexto é projetado para uma instanciação rápida e baixa de sobrecarga:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Use um sinalizador para evitar várias operações simultâneas:

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

  Coloque as operações após a `Loading = true;` linha no `try` bloco.

* Para operações de vida mais longa que aproveitam o [controle de simultaneidade](/ef/core/saving/concurrency), [controle de alterações](/ef/core/querying/tracking) do EF Core, faça [o escopo do contexto para o tempo de vida do componente](#scope-to-the-component-lifetime).

### <a name="new-dbcontext-instances"></a>Novas instâncias de DbContext

A maneira mais rápida de criar uma nova <xref:Microsoft.EntityFrameworkCore.DbContext> instância é usando `new` o para criar uma nova instância. No entanto, há vários cenários que podem exigir a resolução de dependências adicionais. Por exemplo, você pode desejar usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar o contexto.

A solução recomendada para criar um novo <xref:Microsoft.EntityFrameworkCore.DbContext> com dependências é usar uma fábrica. O EF Core 5,0 ou posterior fornece uma fábrica interna para a criação de novos contextos.

O exemplo a seguir configura o [SQLite](https://www.sqlite.org/index.html) e habilita o log de dados. O código usa um método de extensão para configurar a fábrica de banco de dados para DI e fornecer opções padrão:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

A fábrica é injetada em componentes e usada para criar novas instâncias. Por exemplo, em `Pages/Index.razor`:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` é uma [referência de componente](xref:blazor/components/index#capture-references-to-components) para o `GridWrapper` componente. Consulte o `Index` componente ( `Pages/Index.razor` ) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

### <a name="scope-to-the-component-lifetime"></a>Escopo para o tempo de vida do componente

Talvez você queira criar um <xref:Microsoft.EntityFrameworkCore.DbContext> que existe durante o tempo de vida de um componente. Isso permite que você o use como uma [unidade de trabalho](https://martinfowler.com/eaaCatalog/unitOfWork.html) e aproveite os recursos internos, como o controle de alterações e a resolução de simultaneidade.
Você pode usar a fábrica para criar um contexto e rastreá-lo durante o tempo de vida do componente. Primeiro, implemente <xref:System.IDisposable> e insira a fábrica conforme mostrado em `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

O aplicativo de exemplo garante que o contato seja Descartado quando o componente for descartado:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Por fim, [`OnInitializedAsync`](xref:blazor/components/lifecycle) é substituído para criar um novo contexto. No aplicativo de exemplo, o [`OnInitializedAsync`](xref:blazor/components/lifecycle) carrega o contato no mesmo método:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server é uma estrutura de aplicativo com estado. O aplicativo mantém uma conexão contínua com o servidor e o estado do usuário é mantido na memória do servidor em um *circuito*. Um exemplo de estado do usuário são os dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito. O modelo de aplicativo exclusivo que Blazor Server o fornece requer uma abordagem especial para usar Entity Framework Core.

> [!NOTE]
> Este artigo aborda EF Core em Blazor Server aplicativos. Blazor WebAssembly os aplicativos são executados em uma área restrita do Webassembly que impede a maioria das conexões de banco de dados direta. A execução do EF Core no Blazor WebAssembly está além do escopo deste artigo.

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo foi criado como uma referência para Blazor Server aplicativos que usam EF Core. O aplicativo de exemplo inclui uma grade com operações de classificação e filtragem, exclusão, adição e atualização. O exemplo demonstra o uso de EF Core para lidar com a simultaneidade otimista.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([como baixar](xref:index#how-to-download-a-sample))

O exemplo usa um banco de dados [SQLite](https://www.sqlite.org/index.html) local para que ele possa ser usado em qualquer plataforma. O exemplo também configura o log do banco de dados para mostrar as consultas SQL que são geradas. Isso é configurado em `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Os componentes grade, adicionar e exibir usam o padrão "contexto por operação", em que um contexto é criado para cada operação. O componente de edição usa o padrão "contexto por componente", em que um contexto é criado para cada componente.

> [!NOTE]
> Alguns dos exemplos de código neste tópico exigem namespaces e serviços que não são mostrados. Para inspecionar o código totalmente funcional, incluindo as necessárias [`@using`](xref:mvc/views/razor#using) e as [`@inject`](xref:mvc/views/razor#inject) diretivas para obter Razor exemplos, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

## <a name="database-access"></a>Acesso ao banco de dados

EF Core se baseia em um <xref:Microsoft.EntityFrameworkCore.DbContext> como o meio de [Configurar o acesso ao banco de dados](/ef/core/miscellaneous/configuring-dbcontext) e agir como uma [*unidade de trabalho*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core fornece a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extensão para ASP.NET Core aplicativos que registram o contexto como um serviço com *escopo* por padrão. Em Blazor Server aplicativos, isso pode ser problemático porque a instância é compartilhada entre componentes dentro do circuito do usuário. <xref:Microsoft.EntityFrameworkCore.DbContext> Não é thread-safe e não é projetado para uso simultâneo. Os tempos de vida existentes são inadequados por esses motivos:

* **Singleton** compartilha o estado entre todos os usuários do aplicativo e leva a uso simultâneo inadequado.
* Com **escopo** (o padrão) representa um problema semelhante entre os componentes do mesmo usuário.
* Os resultados **transitórios** em uma nova instância por solicitação; Mas como os componentes podem ser de longa duração, isso resulta em um contexto de vida maior do que pode ser pretendido.

## <a name="database-access"></a>Acesso ao banco de dados

As recomendações a seguir foram projetadas para fornecer uma abordagem consistente ao uso de EF Core em Blazor Server aplicativos.

* Por padrão, considere usar um contexto por operação. O contexto é projetado para uma instanciação rápida e baixa de sobrecarga:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Use um sinalizador para evitar várias operações simultâneas:

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

  Coloque as operações após a `Loading = true;` linha no `try` bloco.

* Para operações de vida mais longa que aproveitam o [controle de simultaneidade](/ef/core/saving/concurrency), [controle de alterações](/ef/core/querying/tracking) do EF Core, faça [o escopo do contexto para o tempo de vida do componente](#scope-to-the-component-lifetime).

### <a name="create-new-dbcontext-instances"></a>Criar novas instâncias de DbContext

A maneira mais rápida de criar uma nova <xref:Microsoft.EntityFrameworkCore.DbContext> instância é usando `new` o para criar uma nova instância. No entanto, há vários cenários que podem exigir a resolução de dependências adicionais. Por exemplo, você pode desejar usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar o contexto.

A solução recomendada para criar um novo <xref:Microsoft.EntityFrameworkCore.DbContext> com dependências é usar uma fábrica. O aplicativo de exemplo implementa sua própria fábrica no `Data/DbContextFactory.cs` .

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

Na fábrica anterior, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfaz qualquer dependência por meio do provedor de serviços.

O exemplo a seguir configura o [SQLite](https://www.sqlite.org/index.html) e habilita o log de dados. O código usa um método de extensão para configurar a fábrica de banco de dados para DI e fornecer opções padrão:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

A fábrica é injetada em componentes e usada para criar novas instâncias. Por exemplo, em `Pages/Index.razor`:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` é uma [referência de componente](xref:blazor/components/index#capture-references-to-components) para o `GridWrapper` componente. Consulte o `Index` componente ( `Pages/Index.razor` ) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

### <a name="scope-to-the-component-lifetime"></a>Escopo para o tempo de vida do componente

Talvez você queira criar um <xref:Microsoft.EntityFrameworkCore.DbContext> que existe durante o tempo de vida de um componente. Isso permite que você o use como uma [unidade de trabalho](https://martinfowler.com/eaaCatalog/unitOfWork.html) e aproveite os recursos internos, como o controle de alterações e a resolução de simultaneidade.
Você pode usar a fábrica para criar um contexto e rastreá-lo durante o tempo de vida do componente. Primeiro, implemente <xref:System.IDisposable> e insira a fábrica conforme mostrado em `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

O aplicativo de exemplo garante que o contato seja Descartado quando o componente for descartado:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Por fim, [`OnInitializedAsync`](xref:blazor/components/lifecycle) é substituído para criar um novo contexto. No aplicativo de exemplo, o [`OnInitializedAsync`](xref:blazor/components/lifecycle) carrega o contato no mesmo método:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

No exemplo anterior:

* Quando `Busy` é definido como `true` , as operações assíncronas podem começar. Quando `Busy` é definido de volta como `false` , as operações assíncronas devem ser concluídas.
* Coloque a lógica de tratamento de erro adicional em um `catch` bloco.

:::moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Documentação do EF Core](/ef/)
