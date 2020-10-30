---
title: ASP.NET Core e Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 e posterior funciona com o ASP.NET Core 3,1 e posterior.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/entity-framework-6
ms.openlocfilehash: 086418c161677f585b08ed360555c93d8575e701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059449"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="c437f-103">ASP.NET Core e Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="c437f-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c437f-104">Por [Patrick Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="c437f-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="c437f-105">Usando o Entity Framework 6 com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c437f-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="c437f-106">[Entity Framework Core](/ef/) deve ser usado para um novo desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c437f-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="c437f-107">O [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) usa o [Entity Framework 6 (EF6)](/ef/ef6), que pode ser usado para migrar aplicativos de saída para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c437f-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c437f-108">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c437f-108">Additional resources</span></span>

* [<span data-ttu-id="c437f-109">Entity Framework – configuração baseada em código</span><span class="sxs-lookup"><span data-stu-id="c437f-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c437f-110">Por [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c437f-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="c437f-111">Este artigo mostra como usar o Entity Framework 6 em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c437f-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="c437f-112">Visão geral</span><span class="sxs-lookup"><span data-stu-id="c437f-112">Overview</span></span> 

<span data-ttu-id="c437f-113">Para usar o Entity Framework 6, o projeto precisa ser compilado no .NET Framework, pois o Entity Framework 6 não dá suporte ao .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c437f-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="c437f-114">Caso precise de recursos de multiplataforma, faça upgrade para o [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="c437f-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="c437f-115">A maneira recomendada para usar o Entity Framework 6 em um aplicativo ASP.NET Core é colocar o contexto EF6 e as classes de modelo em um projeto de biblioteca de classes direcionado .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c437f-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="c437f-116">Adicione uma referência à biblioteca de classes do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c437f-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="c437f-117">Consulte a [solução de exemplo do Visual Studio com projetos EF6 e ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="c437f-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="c437f-118">Não é possível colocar um contexto do EF6 em um projeto ASP.NET Core, pois projetos .NET Core não dão suporte a todas as funcionalidades exigidas pelo EF6, como *Enable-Migrations* , que é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="c437f-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="c437f-119">Seja qual for o tipo de projeto em que você localize o contexto do EF6, somente ferramentas de linha de comando do EF6 funcionam com um contexto do EF6.</span><span class="sxs-lookup"><span data-stu-id="c437f-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="c437f-120">Por exemplo, `Scaffold-DbContext` está disponível apenas no Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c437f-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="c437f-121">Se você precisar fazer engenharia reversa de um banco de dados em um modelo EF6, consulte <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> .</span><span class="sxs-lookup"><span data-stu-id="c437f-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="c437f-122">Referenciar a estrutura completa e o EF6 no projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c437f-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="c437f-123">Seu projeto de ASP.NET Core precisa direcionar .NET Framework e fazer referência a EF6.</span><span class="sxs-lookup"><span data-stu-id="c437f-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="c437f-124">Por exemplo, o arquivo *.csproj* do projeto ASP.NET Core será semelhante ao exemplo a seguir (somente as partes relevantes do arquivo são mostradas).</span><span class="sxs-lookup"><span data-stu-id="c437f-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="c437f-125">Ao criar um novo projeto, use o modelo **Aplicativo Web ASP.NET Core (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="c437f-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="c437f-126">Manipular as cadeias de conexão</span><span class="sxs-lookup"><span data-stu-id="c437f-126">Handle connection strings</span></span>    

<span data-ttu-id="c437f-127">As ferramentas de linha de comando do EF6 que você usará no projeto de biblioteca de classes do EF6 exigem um construtor padrão para que possam criar uma instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="c437f-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="c437f-128">No entanto, provavelmente, você desejará especificar a cadeia de conexão a ser usada no projeto ASP.NET Core, caso em que o construtor de contexto deve ter um parâmetro que permita passar a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="c437f-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="c437f-129">Veja um exemplo.</span><span class="sxs-lookup"><span data-stu-id="c437f-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="c437f-130">Como seu contexto EF6 não tem um construtor sem parâmetros, seu projeto EF6 deve fornecer uma implementação de <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> .</span><span class="sxs-lookup"><span data-stu-id="c437f-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="c437f-131">As ferramentas de linha de comando do EF6 encontrarão e usarão essa implementação para que possam criar uma instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="c437f-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="c437f-132">Veja um exemplo.</span><span class="sxs-lookup"><span data-stu-id="c437f-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="c437f-133">Nesse código de exemplo, a implementação `IDbContextFactory` passa uma cadeia de conexão embutida em código.</span><span class="sxs-lookup"><span data-stu-id="c437f-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="c437f-134">Essa é a cadeia de conexão que será usada pelas ferramentas de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="c437f-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="c437f-135">Recomendamos implementar uma estratégia para garantir que a biblioteca de classes use a mesma cadeia de conexão usada pelo aplicativo de chamada.</span><span class="sxs-lookup"><span data-stu-id="c437f-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="c437f-136">Por exemplo, você pode obter o valor de uma variável de ambiente em ambos os projetos.</span><span class="sxs-lookup"><span data-stu-id="c437f-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="c437f-137">Configurar a injeção de dependência no projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c437f-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="c437f-138">No arquivo *Startup.cs* do projeto Core, configure o contexto do EF6 para DI (injeção de dependência) em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c437f-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="c437f-139">Os objetos de contexto do EF devem ser delimitados em escopo a um tempo de vida por solicitação.</span><span class="sxs-lookup"><span data-stu-id="c437f-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="c437f-140">Em seguida, você pode obter uma instância do contexto nos controladores usando a DI.</span><span class="sxs-lookup"><span data-stu-id="c437f-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="c437f-141">O código é semelhante ao que você escreverá para um contexto do EF Core:</span><span class="sxs-lookup"><span data-stu-id="c437f-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="c437f-142">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="c437f-142">Sample application</span></span>   

<span data-ttu-id="c437f-143">Para obter um aplicativo de exemplo funcional, consulte a [solução de exemplo do Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) que acompanha este artigo.</span><span class="sxs-lookup"><span data-stu-id="c437f-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="c437f-144">Esta amostra pode ser criada do zero pelas seguintes etapas no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c437f-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="c437f-145">Crie uma solução.</span><span class="sxs-lookup"><span data-stu-id="c437f-145">Create a solution.</span></span>    

* <span data-ttu-id="c437f-146">**Adicionar** > **Novo Projeto** > **Web** > **Aplicativo Web ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="c437f-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="c437f-147">Na caixa de diálogo de seleção de modelo do projeto, selecione API e .NET Framework na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="c437f-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="c437f-148">**Adicionar** > **Novo Projeto** > **Windows Desktop** > **Biblioteca de Classes (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="c437f-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="c437f-149">No **PMC** (Console do Gerenciador de Pacotes) dos dois projetos, execute o comando `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="c437f-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="c437f-150">No projeto de biblioteca de classes, crie classes de modelo de dados e uma classe de contexto, bem como uma implementação de `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="c437f-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="c437f-151">No PMC do projeto de biblioteca de classes, execute os comandos `Enable-Migrations` e `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="c437f-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="c437f-152">Caso tenha definido o projeto ASP.NET Core como o projeto de inicialização, adicione `-StartupProjectName EF6` a esses comandos.</span><span class="sxs-lookup"><span data-stu-id="c437f-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="c437f-153">No projeto Core, adicione uma referência de projeto ao projeto de biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="c437f-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="c437f-154">No projeto Core, em *Startup.cs* , registre o contexto para DI.</span><span class="sxs-lookup"><span data-stu-id="c437f-154">In the Core project, in *Startup.cs* , register the context for DI.</span></span>    

* <span data-ttu-id="c437f-155">No projeto principal, no *:::no-loc(appsettings.json):::* , adicione a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="c437f-155">In the Core project, in *:::no-loc(appsettings.json):::* , add the connection string.</span></span>  

* <span data-ttu-id="c437f-156">No projeto Core, adicione um controlador e exibições para verificar se é possível ler e gravar dados.</span><span class="sxs-lookup"><span data-stu-id="c437f-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="c437f-157">(Observe que o scaffolding do ASP.NET Core MVC não funcionará com o contexto do EF6 referenciado da biblioteca de classes.)</span><span class="sxs-lookup"><span data-stu-id="c437f-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
