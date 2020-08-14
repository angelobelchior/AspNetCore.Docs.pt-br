---
title: ASP.NET Core e Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 e posterior funciona com o ASP.NET Core 3,1 e posterior.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: c65a1ae7cc67ec7e15a2b732184bc05803eb40ce
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202764"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="87579-103">ASP.NET Core e Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="87579-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87579-104">[Entity Framework](/ef/ef6/) 6,3 e posterior funciona com o ASP.NET Core 3,1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="87579-104">[Entity Framework](/ef/ef6/) 6.3 and later works with ASP.NET Core 3.1 and later.</span></span> <span data-ttu-id="87579-105">Para obter mais informações, consulte a postagem no blog [anunciando Entity Framework 6,3 com suporte ao .NET Core](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span><span class="sxs-lookup"><span data-stu-id="87579-105">For more information, see the blog post [Announcing Entity Framework 6.3 with .NET Core Support](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87579-106">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="87579-106">Additional resources</span></span>

* [<span data-ttu-id="87579-107">Entity Framework – configuração baseada em código</span><span class="sxs-lookup"><span data-stu-id="87579-107">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87579-108">Por [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="87579-108">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="87579-109">Este artigo mostra como usar o Entity Framework 6 em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87579-109">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="87579-110">Visão geral</span><span class="sxs-lookup"><span data-stu-id="87579-110">Overview</span></span> 

<span data-ttu-id="87579-111">Para usar o Entity Framework 6, o projeto precisa ser compilado no .NET Framework, pois o Entity Framework 6 não dá suporte ao .NET Core.</span><span class="sxs-lookup"><span data-stu-id="87579-111">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="87579-112">Caso precise de recursos de multiplataforma, faça upgrade para o [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="87579-112">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="87579-113">A maneira recomendada para usar o Entity Framework 6 em um aplicativo ASP.NET Core é colocar o contexto EF6 e as classes de modelo em um projeto de biblioteca de classes direcionado .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="87579-113">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="87579-114">Adicione uma referência à biblioteca de classes do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87579-114">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="87579-115">Consulte a [solução de exemplo do Visual Studio com projetos EF6 e ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="87579-115">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="87579-116">Não é possível colocar um contexto do EF6 em um projeto ASP.NET Core, pois projetos .NET Core não dão suporte a todas as funcionalidades exigidas pelo EF6, como *Enable-Migrations*, que é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="87579-116">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="87579-117">Seja qual for o tipo de projeto em que você localize o contexto do EF6, somente ferramentas de linha de comando do EF6 funcionam com um contexto do EF6.</span><span class="sxs-lookup"><span data-stu-id="87579-117">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="87579-118">Por exemplo, `Scaffold-DbContext` está disponível apenas no Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="87579-118">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="87579-119">Caso precise fazer engenharia reversa de um banco de dados para um modelo do EF6, consulte [Usar o Code First para um banco de dados existente](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="87579-119">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>  

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="87579-120">Referenciar a estrutura completa e o EF6 no projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87579-120">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="87579-121">Seu projeto de ASP.NET Core precisa direcionar .NET Framework e fazer referência a EF6.</span><span class="sxs-lookup"><span data-stu-id="87579-121">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="87579-122">Por exemplo, o arquivo *.csproj* do projeto ASP.NET Core será semelhante ao exemplo a seguir (somente as partes relevantes do arquivo são mostradas).</span><span class="sxs-lookup"><span data-stu-id="87579-122">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="87579-123">Ao criar um novo projeto, use o modelo **Aplicativo Web ASP.NET Core (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="87579-123">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="87579-124">Manipular as cadeias de conexão</span><span class="sxs-lookup"><span data-stu-id="87579-124">Handle connection strings</span></span>    

<span data-ttu-id="87579-125">As ferramentas de linha de comando do EF6 que você usará no projeto de biblioteca de classes do EF6 exigem um construtor padrão para que possam criar uma instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="87579-125">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="87579-126">No entanto, provavelmente, você desejará especificar a cadeia de conexão a ser usada no projeto ASP.NET Core, caso em que o construtor de contexto deve ter um parâmetro que permita passar a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="87579-126">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="87579-127">Veja um exemplo.</span><span class="sxs-lookup"><span data-stu-id="87579-127">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="87579-128">Como o contexto do EF6 não tem um construtor sem parâmetros, o projeto do EF6 precisa fornecer uma implementação de [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="87579-128">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="87579-129">As ferramentas de linha de comando do EF6 encontrarão e usarão essa implementação para que possam criar uma instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="87579-129">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="87579-130">Veja um exemplo.</span><span class="sxs-lookup"><span data-stu-id="87579-130">Here's an example.</span></span>    

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="87579-131">Nesse código de exemplo, a implementação `IDbContextFactory` passa uma cadeia de conexão embutida em código.</span><span class="sxs-lookup"><span data-stu-id="87579-131">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="87579-132">Essa é a cadeia de conexão que será usada pelas ferramentas de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="87579-132">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="87579-133">Recomendamos implementar uma estratégia para garantir que a biblioteca de classes use a mesma cadeia de conexão usada pelo aplicativo de chamada.</span><span class="sxs-lookup"><span data-stu-id="87579-133">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="87579-134">Por exemplo, você pode obter o valor de uma variável de ambiente em ambos os projetos.</span><span class="sxs-lookup"><span data-stu-id="87579-134">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="87579-135">Configurar a injeção de dependência no projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87579-135">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="87579-136">No arquivo *Startup.cs* do projeto Core, configure o contexto do EF6 para DI (injeção de dependência) em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="87579-136">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="87579-137">Os objetos de contexto do EF devem ser delimitados em escopo a um tempo de vida por solicitação.</span><span class="sxs-lookup"><span data-stu-id="87579-137">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="87579-138">Em seguida, você pode obter uma instância do contexto nos controladores usando a DI.</span><span class="sxs-lookup"><span data-stu-id="87579-138">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="87579-139">O código é semelhante ao que você escreverá para um contexto do EF Core:</span><span class="sxs-lookup"><span data-stu-id="87579-139">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="87579-140">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="87579-140">Sample application</span></span>   

<span data-ttu-id="87579-141">Para obter um aplicativo de exemplo funcional, consulte a [solução de exemplo do Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) que acompanha este artigo.</span><span class="sxs-lookup"><span data-stu-id="87579-141">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="87579-142">Esta amostra pode ser criada do zero pelas seguintes etapas no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="87579-142">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="87579-143">Crie uma solução.</span><span class="sxs-lookup"><span data-stu-id="87579-143">Create a solution.</span></span>    

* <span data-ttu-id="87579-144">**Adicionar** > **Novo Projeto** > **Web** > **Aplicativo Web ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="87579-144">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="87579-145">Na caixa de diálogo de seleção de modelo do projeto, selecione API e .NET Framework na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="87579-145">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="87579-146">**Adicionar** > **Novo Projeto** > **Windows Desktop** > **Biblioteca de Classes (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="87579-146">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="87579-147">No **PMC** (Console do Gerenciador de Pacotes) dos dois projetos, execute o comando `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="87579-147">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="87579-148">No projeto de biblioteca de classes, crie classes de modelo de dados e uma classe de contexto, bem como uma implementação de `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="87579-148">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="87579-149">No PMC do projeto de biblioteca de classes, execute os comandos `Enable-Migrations` e `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="87579-149">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="87579-150">Caso tenha definido o projeto ASP.NET Core como o projeto de inicialização, adicione `-StartupProjectName EF6` a esses comandos.</span><span class="sxs-lookup"><span data-stu-id="87579-150">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="87579-151">No projeto Core, adicione uma referência de projeto ao projeto de biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="87579-151">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="87579-152">No projeto Core, em *Startup.cs*, registre o contexto para DI.</span><span class="sxs-lookup"><span data-stu-id="87579-152">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="87579-153">No projeto Core, em *appsettings.json*, adicione a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="87579-153">In the Core project, in *appsettings.json*, add the connection string.</span></span>    

* <span data-ttu-id="87579-154">No projeto Core, adicione um controlador e exibições para verificar se é possível ler e gravar dados.</span><span class="sxs-lookup"><span data-stu-id="87579-154">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="87579-155">(Observe que o scaffolding do ASP.NET Core MVC não funcionará com o contexto do EF6 referenciado da biblioteca de classes.)</span><span class="sxs-lookup"><span data-stu-id="87579-155">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
