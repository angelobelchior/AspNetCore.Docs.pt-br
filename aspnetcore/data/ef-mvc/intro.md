---
title: 'Tutorial: introdução ao EF Core em um aplicativo Web ASP.NET MVC'
description: Esta página é a primeira de uma série de tutoriais que explicam como criar o aplicativo de exemplo do EF/MVC da Contoso University "
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365361"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="e7e3d-103">Tutorial: introdução ao EF Core em um aplicativo Web ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="e7e3d-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="e7e3d-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7e3d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="e7e3d-105">O AP da Web de exemplo da Contoso University demonstra como criar um aplicativo Web ASP.NET Core MVC usando o Entity Framework (EF) Core e o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="e7e3d-106">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e7e3d-107">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e7e3d-108">Este é o primeiro de uma série de tutoriais que explicam como criar o aplicativo de exemplo da Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7e3d-109">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e7e3d-109">Prerequisites</span></span>

* <span data-ttu-id="e7e3d-110">Se você for novo no ASP.NET Core MVC, consulte a série de tutoriais [introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) antes de iniciar este.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="e7e3d-111">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-111">Database engines</span></span>

<span data-ttu-id="e7e3d-112">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="e7e3d-113">Solucionar problemas e solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="e7e3d-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="e7e3d-114">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="e7e3d-115">Para obter uma lista de erros comuns e como resolvê-los, consulte [a seção Solução de problemas do último tutorial da série](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="e7e3d-116">Caso não encontre o que precisa na seção, poste uma pergunta no StackOverflow.com sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="e7e3d-117">Esta é uma série de dez tutoriais, cada um se baseando no que é feito nos tutoriais anteriores.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="e7e3d-118">Considere a possibilidade de salvar uma cópia do projeto após a conclusão bem-sucedida de cada tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="e7e3d-119">Caso tenha problemas, comece novamente no tutorial anterior em vez de voltar ao início de toda a série.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="e7e3d-120">Aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="e7e3d-120">Contoso University web app</span></span>

<span data-ttu-id="e7e3d-121">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="e7e3d-122">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e7e3d-123">Aqui estão algumas das telas no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-123">Here are a few of the screens in the app:</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="e7e3d-126">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="e7e3d-126">Create web app</span></span>

* <span data-ttu-id="e7e3d-127">Inicie o Visual Studio e selecione **ASP.NET Core aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="e7e3d-128">Dê ao projeto o nome de `ContosoUniversity`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="e7e3d-129">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código é copiado.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="e7e3d-130">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-130">Select **Create**.</span></span>
* <span data-ttu-id="e7e3d-131">Selecione **.NET Core** e **ASP.NET Core 5,0** nos menus suspensos e, em seguida, selecione modelo de **aplicativo Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="e7e3d-132">![Caixa de diálogo Novo projeto ASP.NET Core](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="e7e3d-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="e7e3d-133">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="e7e3d-133">Set up the site style</span></span>

<span data-ttu-id="e7e3d-134">Algumas alterações básicas configuram o menu, layout e home page do site.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="e7e3d-135">Abra *Views/Shared/_Layout.cshtml* e faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="e7e3d-136">Altere cada ocorrência de `ContosoUniversity` para `Contoso University` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="e7e3d-137">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-137">There are three occurrences.</span></span>
* <span data-ttu-id="e7e3d-138">Adicione entradas de menu **Sobre** , **Alunos** , **Cursos** , **Instrutores** e **Departamentos** e exclua a entrada de menu **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="e7e3d-139">As alterações anteriores são realçadas no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="e7e3d-140">Em *views/home/index. cshtml* , substitua o conteúdo do arquivo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="e7e3d-141">Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="e7e3d-142">O home page é exibido com guias para as páginas criadas neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Home page da Contoso University](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="e7e3d-144">EF Core pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="e7e3d-144">EF Core NuGet packages</span></span>

<span data-ttu-id="e7e3d-145">Este tutorial usa o SQL Server e o pacote de provedor é [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="e7e3d-146">O pacote de SQL Server do EF e suas dependências e `Microsoft.EntityFrameworkCore` `Microsoft.EntityFrameworkCore.Relational` fornecem suporte de tempo de execução para o EF.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="e7e3d-147">Adicione o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) e o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="e7e3d-148">No console do Gerenciador de programas (PMC), digite os seguintes comandos para adicionar os pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="e7e3d-149">O `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacote NuGet fornece ASP.NET Core middleware para EF Core páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="e7e3d-150">Esse middleware ajuda a detectar e diagnosticar erros com EF Core migrações.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="e7e3d-151">Para obter informações sobre outros provedores de banco de dados que estão disponíveis para EF Core, consulte [provedores de banco de dados](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="e7e3d-152">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-152">Create the data model</span></span>

<span data-ttu-id="e7e3d-153">As classes de entidade a seguir são criadas para este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-153">The following entity classes are created for this app:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="e7e3d-155">As entidades anteriores têm as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="e7e3d-156">Uma relação um-para-muitos entre `Student` as `Enrollment` entidades e.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="e7e3d-157">Um aluno pode ser registrado em qualquer número de cursos.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="e7e3d-158">Uma relação um-para-muitos entre `Course` as `Enrollment` entidades e.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e7e3d-159">Um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e7e3d-160">Nas seções a seguir, uma classe é criada para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e7e3d-161">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="e7e3d-161">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="e7e3d-163">Na pasta *modelos* , crie a `Student` classe com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-164">A `ID` propriedade é a coluna de chave primária ( **CP** ) da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="e7e3d-165">Por padrão, o EF interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="e7e3d-166">Por exemplo, a CP poderia ser nomeada `StudentID` em vez de `ID` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="e7e3d-167">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e7e3d-168">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="e7e3d-169">A `Enrollments` propriedade de uma `Student` entidade:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="e7e3d-170">Contém todas as `Enrollment` entidades relacionadas a essa `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="e7e3d-171">Se uma `Student` linha específica no banco de dados tiver duas `Enrollment` linhas relacionadas:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="e7e3d-172">A `Student` propriedade de `Enrollments` navegação dessa entidade contém essas duas `Enrollment` entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="e7e3d-173">`Enrollment` as linhas contêm o valor de CP de um aluno na `StudentID` coluna de chave estrangeira ( **FK** ).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="e7e3d-174">Se uma propriedade de navegação puder conter várias entidades:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="e7e3d-175">O tipo deve ser uma lista, como `ICollection<T>` , `List<T>` ou `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="e7e3d-176">As entidades podem ser adicionadas, excluídas e atualizadas.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="e7e3d-177">Relações de navegação muitos para muitos e um para muitos podem conter várias entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="e7e3d-178">Quando `ICollection<T>` é usado, o EF cria uma `HashSet<T>` coleção por padrão.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e7e3d-179">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="e7e3d-179">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="e7e3d-181">Na pasta *modelos* , crie a `Enrollment` classe com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-182">A `EnrollmentID` propriedade é a CP.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="e7e3d-183">Essa entidade usa o `classnameID` padrão em vez de `ID` por si só.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="e7e3d-184">A `Student` entidade usou o `ID` padrão.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="e7e3d-185">Alguns desenvolvedores preferem usar um padrão em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="e7e3d-186">Neste tutorial, a variação ilustra que qualquer padrão pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="e7e3d-187">Um [tutorial posterior](inheritance.md) mostra como o uso `ID` do sem ClassName facilita a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e7e3d-188">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e7e3d-189">O `?` após a `Grade` declaração de tipo indica que a `Grade` propriedade é [anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="e7e3d-190">Uma classificação que `null` é diferente de uma classificação zero.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="e7e3d-191">`null` significa que uma classificação não é conhecida ou ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e7e3d-192">A `StudentID` propriedade é uma chave estrangeira (FK) e a propriedade de navegação correspondente é `Student` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e7e3d-193">Uma `Enrollment` entidade está associada a uma `Student` entidade, portanto, a propriedade só pode conter uma única `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="e7e3d-194">Isso é diferente da `Student.Enrollments` propriedade de navegação, que pode conter várias `Enrollment` entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="e7e3d-195">A `CourseID` propriedade é um FK e a propriedade de navegação correspondente é `Course` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e7e3d-196">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e7e3d-197">Entity Framework interpretará uma propriedade como uma propriedade de FK se for nomeada nome da `<` propriedade de navegação `><` nome da propriedade de chave primária `>` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="e7e3d-198">Por exemplo, `StudentID` para a `Student` propriedade de navegação, uma vez que a `Student` CP da entidade é `ID` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="e7e3d-199">As propriedades FK também podem ser nomeadas  `<` como nome da propriedade de chave primária `>` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="e7e3d-200">Por exemplo, `CourseID` como a `Course` CP da entidade é `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e7e3d-201">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="e7e3d-201">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="e7e3d-203">Na pasta *modelos* , crie a `Course` classe com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-204">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e7e3d-205">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e7e3d-206">O atributo [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) é explicado em um [tutorial posterior](complex-data-model.md).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="e7e3d-207">Esse atributo permite inserir a CP para o curso em vez de fazer com que o banco de dados a gere.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="e7e3d-208">Criar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-208">Create the database context</span></span>

<span data-ttu-id="e7e3d-209">A classe principal que coordena a funcionalidade do EF para um determinado modelo de dados é a <xref:Microsoft.EntityFrameworkCore.DbContext> classe de contexto Database.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="e7e3d-210">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="e7e3d-211">A `DbContext` classe derivada especifica quais entidades estão incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="e7e3d-212">Alguns comportamentos do EF podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="e7e3d-213">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e7e3d-214">Na pasta do projeto, crie uma pasta chamada `Data` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="e7e3d-215">Na pasta *dados* , crie uma `SchoolContext` classe com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-216">O código anterior cria uma `DbSet` propriedade para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="e7e3d-217">Na terminologia do EF:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-217">In EF terminology:</span></span>

* <span data-ttu-id="e7e3d-218">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="e7e3d-219">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e7e3d-220">As `DbSet<Enrollment>` `DbSet<Course>` instruções e poderiam ser omitidas e funcionarão da mesma.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="e7e3d-221">O EF os incluiria implicitamente porque:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="e7e3d-222">A `Student` entidade referencia a `Enrollment` entidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="e7e3d-223">A `Enrollment` entidade referencia a `Course` entidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="e7e3d-224">Quando o banco de dados é criado, o EF cria tabelas que têm nomes iguais aos nomes de propriedade `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="e7e3d-225">Os nomes de propriedade das coleções normalmente são plural.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="e7e3d-226">Por exemplo, `Students` em vez de `Student` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="e7e3d-227">Os desenvolvedores não concordam sobre se os nomes de tabela devem ser pluralizados ou não.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="e7e3d-228">Para esses tutoriais, o comportamento padrão é substituído pela especificação de nomes de tabela singulares no `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="e7e3d-229">Para fazer isso, adicione o código realçado a seguir após a última propriedade DbSet.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="e7e3d-230">Registrar o `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="e7e3d-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="e7e3d-231">O ASP.NET Core inclui a [injeção de dependência](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="e7e3d-232">Serviços, como o contexto de banco de dados do EF, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="e7e3d-233">Os componentes que exigem esses serviços, como controladores MVC, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="e7e3d-234">O código do construtor do controlador que obtém uma instância de contexto é mostrado posteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="e7e3d-235">Para registrar `SchoolContext` como um serviço, abra *Startup.cs* e adicione as linhas realçadas ao método `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="e7e3d-236">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="e7e3d-237">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="e7e3d-238">Abra o *:::no-loc(appsettings.json):::* arquivo e adicione uma cadeia de conexão, conforme mostrado na seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-238">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="e7e3d-239">Adicionar o filtro de exceção de banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-239">Add the database exception filter</span></span>

<span data-ttu-id="e7e3d-240">Adicione <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> ao `ConfigureServices` conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="e7e3d-241">O `AddDatabaseDeveloperPageExceptionFilter` fornece informações de erro úteis no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e7e3d-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e7e3d-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e7e3d-243">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="e7e3d-244">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="e7e3d-245">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e7e3d-246">Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="e7e3d-247">Inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="e7e3d-247">Initialize DB with test data</span></span>

<span data-ttu-id="e7e3d-248">O EF cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-248">EF creates an empty database.</span></span> <span data-ttu-id="e7e3d-249">Nesta seção, é adicionado um método que é chamado depois que o banco de dados é criado para preenchê-lo com dado de teste.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="e7e3d-250">O `EnsureCreated` método é usado para criar automaticamente o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="e7e3d-251">Em um [tutorial posterior](migrations.md), você verá como lidar com alterações de modelo usando migrações do Code First para alterar o esquema de banco de dados em vez de descartar e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="e7e3d-252">Na pasta *dados* , crie uma nova classe chamada `DbInitializer` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="e7e3d-253">O código anterior verifica se o banco de dados existe:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="e7e3d-254">Se o banco de dados não for encontrado;</span><span class="sxs-lookup"><span data-stu-id="e7e3d-254">If the database is not found;</span></span>
  * <span data-ttu-id="e7e3d-255">Ele é criado e carregado com dados de teste.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-255">It is created and loaded with test data.</span></span> <span data-ttu-id="e7e3d-256">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="e7e3d-257">Se o banco de dados for encontrado, ele não executará nenhuma ação.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="e7e3d-258">Atualize *Program.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="e7e3d-259">*Program.cs* faz o seguinte na inicialização do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="e7e3d-260">Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e7e3d-261">Chame o método `DbInitializer.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="e7e3d-262">Descarte o contexto quando o `Initialize` método for concluído, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="e7e3d-263">Na primeira vez em que o aplicativo é executado, o banco de dados é criado e carregado com o teste Data.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="e7e3d-264">Sempre que o modelo de dados for alterado:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="e7e3d-265">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-265">Delete the database.</span></span>
* <span data-ttu-id="e7e3d-266">Atualize o método semente e inicie o novamente com um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="e7e3d-267">Nos tutoriais posteriores, o banco de dados é modificado quando o modelo de dado é alterado, sem excluí-lo e recriá-lo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="e7e3d-268">Nenhum dado é perdido quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="e7e3d-269">Criar um controlador e exibições</span><span class="sxs-lookup"><span data-stu-id="e7e3d-269">Create controller and views</span></span>

<span data-ttu-id="e7e3d-270">Use o mecanismo scaffolding no Visual Studio para adicionar um controlador MVC e exibições que usarão o EF para consultar e salvar dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="e7e3d-271">A criação automática de métodos e exibições de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) é conhecida como scaffolding.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="e7e3d-272">Em **Gerenciador de soluções** , clique com o botão direito do mouse na `Controllers` pasta e selecione **Adicionar > novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="e7e3d-273">Na caixa de diálogo **Adicionar Scaffolding** :</span><span class="sxs-lookup"><span data-stu-id="e7e3d-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="e7e3d-274">Selecione **Controlador MVC com exibições, usando o Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="e7e3d-275">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-275">Click **Add**.</span></span> <span data-ttu-id="e7e3d-276">A caixa de diálogo **Adicionar controlador MVC com exibições, usando Entity Framework** é exibida: ![ Scaffold Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="e7e3d-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="e7e3d-277">Em **classe de modelo** , selecione **aluno**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="e7e3d-278">Na **classe de contexto de dados** , selecione **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="e7e3d-279">Aceite o **StudentsController** padrão como o nome.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="e7e3d-280">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-280">Click **Add**.</span></span>

<span data-ttu-id="e7e3d-281">O mecanismo de scaffolding do Visual Studio cria um `StudentsController.cs` arquivo e um conjunto de exibições ( `*.cshtml` arquivos) que funcionam com o controlador.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="e7e3d-282">Observe que o controlador usa `SchoolContext` como um parâmetro de construtor.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="e7e3d-283">A injeção de dependência do ASP.NET Core é responsável por passar uma instância de `SchoolContext` para o controlador.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="e7e3d-284">Você configurou isso na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="e7e3d-285">O controlador contém um método de ação `Index`, que exibe todos os alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="e7e3d-286">O método obtém uma lista de alunos do conjunto de entidades Students pela leitura da propriedade `Students` da instância de contexto de banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="e7e3d-287">Os elementos de programação assíncrona neste código são explicados posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="e7e3d-288">A exibição *Views/Students/Index.cshtml* mostra esta lista em uma tabela:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="e7e3d-289">Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="e7e3d-290">Clique na guia Alunos para ver os dados de teste inserido pelo método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="e7e3d-291">Dependendo da largura da janela do navegador, você verá o link da guia `Students` na parte superior da página ou precisará clicar no ícone de navegação no canto superior direito para ver o link.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Home page estreita da Contoso University](intro/_static/home-page-narrow.png)

![Página Índice de Alunos](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="e7e3d-294">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-294">View the database</span></span>

<span data-ttu-id="e7e3d-295">Quando o aplicativo é iniciado, o `DbInitializer.Initialize` método chama `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="e7e3d-296">O EF observou que não havia nenhum banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="e7e3d-297">Então, ele criou um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-297">So it created a database.</span></span>
* <span data-ttu-id="e7e3d-298">O `Initialize` código do método preencheu o banco de dados com os Data.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="e7e3d-299">Use **pesquisador de objetos do SQL Server** (SSOX) para exibir o banco de dados no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="e7e3d-300">Selecione **pesquisador de objetos do SQL Server** no menu **Exibir** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="e7e3d-301">Em SSOX, selecione **(LocalDB) \MSSQLLocalDB bancos de dados de >**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="e7e3d-302">Selecione `ContosoUniversity1` , a entrada para o nome do banco de dados que está na cadeia de conexão no *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="e7e3d-303">Expanda o nó **tabelas** para ver as tabelas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-303">Expand the **Tables** node to see the tables in the database.</span></span>

![Tabelas no SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="e7e3d-305">Clique com o botão direito do mouse na tabela **Student** e clique em **exibir dados** para ver os dados na tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![Tabela Aluno no SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="e7e3d-307">Os `*.mdf` `*.ldf` arquivos de banco de dados e estão na pasta *C:\Users \\ \<username>* .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="e7e3d-308">Como `EnsureCreated` é chamado no método inicializador que é executado no início do aplicativo, você pode:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="e7e3d-309">Faça uma alteração na `Student` classe.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="e7e3d-310">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-310">Delete the database.</span></span>
* <span data-ttu-id="e7e3d-311">Pare e inicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-311">Stop, then start the app.</span></span> <span data-ttu-id="e7e3d-312">O banco de dados é recriado automaticamente para corresponder à alteração.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="e7e3d-313">Por exemplo, se uma `EmailAddress` propriedade for adicionada à `Student` classe, uma nova `EmailAddress` coluna na tabela recriada.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="e7e3d-314">A exibição em classe não exibirá a nova `EmailAddress` propriedade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="e7e3d-315">Convenções</span><span class="sxs-lookup"><span data-stu-id="e7e3d-315">Conventions</span></span>

<span data-ttu-id="e7e3d-316">A quantidade de código escrita para que o EF crie um banco de dados completo é mínima devido ao uso das convenções que o EF usa:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="e7e3d-317">Os nomes de propriedades `DbSet` são usadas como nomes de tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="e7e3d-318">Para entidades não referenciadas por uma propriedade `DbSet`, os nomes de classe de entidade são usados como nomes de tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="e7e3d-319">Os nomes de propriedade de entidade são usados para nomes de coluna.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="e7e3d-320">Propriedades de entidade que são nomeadas `ID` ou `classnameID` são reconhecidas como propriedades de CP.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="e7e3d-321">Uma propriedade será interpretada como uma propriedade de FK se for nomeada nome da `<` propriedade de navegação `><` nome da propriedade CP `>` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="e7e3d-322">Por exemplo, `StudentID` para a `Student` propriedade de navegação, uma vez que a `Student` CP da entidade é `ID` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="e7e3d-323">As propriedades FK também podem ser nomeadas `<` como nome da propriedade de chave primária `>` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="e7e3d-324">Por exemplo, `EnrollmentID` como a `Enrollment` CP da entidade é `EnrollmentID` .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="e7e3d-325">O comportamento convencional pode ser substituído.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="e7e3d-326">Por exemplo, os nomes de tabela podem ser especificados explicitamente, conforme mostrado anteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="e7e3d-327">Os nomes de coluna e qualquer propriedade podem ser definidos como uma CP ou FK.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e7e3d-328">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="e7e3d-328">Asynchronous code</span></span>

<span data-ttu-id="e7e3d-329">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e7e3d-330">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e7e3d-331">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e7e3d-332">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e7e3d-333">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e7e3d-334">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e7e3d-335">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, ao passo que, em situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e7e3d-336">No código a seguir, `async` , `Task<T>` , `await` e faça com que `ToListAsync` o código seja executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e7e3d-337">A palavra-chave `async` instrui o compilador a gerar retornos de chamada para partes do corpo do método e a criar automaticamente o objeto `Task<IActionResult>` que é retornado.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="e7e3d-338">O tipo de retorno `Task<IActionResult>` representa um trabalho em andamento com um resultado do tipo `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="e7e3d-339">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e7e3d-340">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e7e3d-341">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="e7e3d-342">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e7e3d-343">Algumas coisas a serem consideradas ao escrever código assíncrono que usa o EF:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="e7e3d-344">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="e7e3d-345">Isso inclui, por exemplo, `ToListAsync`, `SingleOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e7e3d-346">Isso não inclui, por exemplo, instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="e7e3d-347">Um contexto do EF não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="e7e3d-348">Quando você chamar qualquer método assíncrono do EF, sempre use a palavra-chave `await`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="e7e3d-349">Para aproveitar os benefícios de desempenho do código assíncrono, certifique-se de que qualquer pacote de biblioteca usado também use Async se eles chamarem quaisquer métodos do EF que façam com que as consultas sejam enviadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="e7e3d-350">Para obter mais informações sobre a programação assíncrona no .NET, consulte [Visão geral da programação assíncrona](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="e7e3d-351">Limitar entidades buscadas</span><span class="sxs-lookup"><span data-stu-id="e7e3d-351">Limit entities fetched</span></span>

<span data-ttu-id="e7e3d-352">Consulte [Considerações sobre desempenho](xref:data/ef-rp/intro) para obter informações sobre como limitar o número ou as entidades retornadas de uma consulta.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="e7e3d-353">Vá para o próximo tutorial para aprender a executar operações CRUD (criar, ler, atualizar e excluir) básicas.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e7e3d-354">Implementar a funcionalidade CRUD básica</span><span class="sxs-lookup"><span data-stu-id="e7e3d-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="e7e3d-355">O aplicativo Web de exemplo Contoso University demonstra como criar aplicativos Web em ASP.NET Core 2.2 MVC usando o EF (Entity Framework) Core 2.2 e o Visual Studio 2017 ou 2019.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="e7e3d-356">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e7e3d-357">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e7e3d-358">Este é o primeiro de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University do zero.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7e3d-359">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e7e3d-359">Prerequisites</span></span>

* [<span data-ttu-id="e7e3d-360">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="e7e3d-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="e7e3d-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com as cargas de trabalho a seguir:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="e7e3d-362">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="e7e3d-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="e7e3d-363">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="e7e3d-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e7e3d-364">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="e7e3d-364">Troubleshooting</span></span>

<span data-ttu-id="e7e3d-365">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="e7e3d-366">Para obter uma lista de erros comuns e como resolvê-los, consulte [a seção Solução de problemas do último tutorial da série](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="e7e3d-367">Caso não encontre o que precisa na seção, poste uma pergunta no StackOverflow.com sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="e7e3d-368">Esta é uma série de dez tutoriais, cada um se baseando no que é feito nos tutoriais anteriores.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="e7e3d-369">Considere a possibilidade de salvar uma cópia do projeto após a conclusão bem-sucedida de cada tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="e7e3d-370">Caso tenha problemas, comece novamente no tutorial anterior em vez de voltar ao início de toda a série.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="e7e3d-371">Aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="e7e3d-371">Contoso University web app</span></span>

<span data-ttu-id="e7e3d-372">O aplicativo que você criará nestes tutoriais é um site simples de uma universidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="e7e3d-373">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e7e3d-374">Estas são algumas das telas que você criará.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-374">Here are a few of the screens you'll create.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="e7e3d-377">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="e7e3d-377">Create web app</span></span>

* <span data-ttu-id="e7e3d-378">Abra o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-378">Open Visual Studio.</span></span>

* <span data-ttu-id="e7e3d-379">No menu **Arquivo** , selecione **Novo > Projeto**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="e7e3d-380">No painel esquerdo, selecione **Instalado > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="e7e3d-381">Selecione o modelo de projeto **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="e7e3d-382">Insira **ContosoUniversity** como o nome e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Caixa de diálogo Novo Projeto](intro/_static/new-project2.png)

* <span data-ttu-id="e7e3d-384">Aguarde a exibição da caixa de diálogo **novo ASP.NET Core aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="e7e3d-385">Selecione **.NET Core** , **ASP.NET Core 2.2** e o modelo **Aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="e7e3d-386">Verifique se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="e7e3d-387">Selecione **OK**</span><span class="sxs-lookup"><span data-stu-id="e7e3d-387">Select **OK**</span></span>

  ![Caixa de diálogo Novo projeto ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="e7e3d-389">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="e7e3d-389">Set up the site style</span></span>

<span data-ttu-id="e7e3d-390">Algumas alterações simples configurarão o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="e7e3d-391">Abra *Views/Shared/_Layout.cshtml* e faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="e7e3d-392">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="e7e3d-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e7e3d-393">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-393">There are three occurrences.</span></span>

* <span data-ttu-id="e7e3d-394">Adicione entradas de menu **Sobre** , **Alunos** , **Cursos** , **Instrutores** e **Departamentos** e exclua a entrada de menu **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="e7e3d-395">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="e7e3d-396">Em *Views/Home/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="e7e3d-397">Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="e7e3d-398">Você verá a home page com guias para as páginas que você criará nestes tutoriais.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Home page da Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="e7e3d-400">Sobre pacotes NuGet do EF Core</span><span class="sxs-lookup"><span data-stu-id="e7e3d-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="e7e3d-401">Para adicionar o suporte do EF Core a um projeto, instale o provedor de banco de dados que você deseja ter como destino.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="e7e3d-402">Este tutorial usa o SQL Server e o pacote de provedor é [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="e7e3d-403">Esse pacote está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), portanto, você não precisa referenciar o pacote.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="e7e3d-404">O pacote SQL Server do EF e suas dependências (`Microsoft.EntityFrameworkCore` e `Microsoft.EntityFrameworkCore.Relational`) fornecem suporte de runtime para o EF.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="e7e3d-405">Você adicionará um pacote de ferramentas posteriormente, no tutorial [Migrações](migrations.md).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="e7e3d-406">Para obter informações sobre outros provedores de banco de dados que estão disponíveis para o Entity Framework Core, consulte [Provedores de banco de dados](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="e7e3d-407">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-407">Create the data model</span></span>

<span data-ttu-id="e7e3d-408">Em seguida, você criará as classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="e7e3d-409">Você começará com as três entidades a seguir.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-409">You'll start with the following three entities.</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="e7e3d-411">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`, e uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e7e3d-412">Em outras palavras, um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e7e3d-413">Nas seções a seguir, você criará uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e7e3d-414">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="e7e3d-414">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="e7e3d-416">Na pasta *Models* , crie um arquivo de classe chamado *Student.cs* e substitua o código de modelo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-417">A propriedade `ID` se tornará a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="e7e3d-418">Por padrão, o Entity Framework interpreta uma propriedade chamada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="e7e3d-419">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e7e3d-420">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="e7e3d-421">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazenará todas as entidades `Enrollment` relacionadas a essa entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="e7e3d-422">Em outras palavras, se uma `Student` linha no banco de dados tiver duas `Enrollment` linhas relacionadas (linhas que contêm o valor de chave primária do aluno em sua coluna de chave estrangeira StudentId), a `Student` propriedade de navegação dessa entidade `Enrollments` conterá essas duas `Enrollment` entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="e7e3d-423">Se uma propriedade de navegação pode armazenar várias entidades (como em relações muitos para muitos ou um-para-muitos), o tipo precisa ser uma lista na qual entradas podem ser adicionadas, excluídas e atualizadas, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="e7e3d-424">Especifique `ICollection<T>` ou um tipo, como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e7e3d-425">Se você especificar `ICollection<T>`, o EF criará uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e7e3d-426">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="e7e3d-426">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="e7e3d-428">Na pasta *Models* , crie *Enrollment.cs* e substitua o código existente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-429">A propriedade `EnrollmentID` será a chave primária; essa entidade usa o padrão `classnameID` em vez de `ID` por si só, como você viu na entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="e7e3d-430">Normalmente, você escolhe um padrão e usa-o em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="e7e3d-431">Aqui, a variação ilustra que você pode usar qualquer um dos padrões.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="e7e3d-432">Em um [tutorial posterior](inheritance.md), você verá como usar uma ID sem nome de classe facilita a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e7e3d-433">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e7e3d-434">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="e7e3d-435">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e7e3d-436">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e7e3d-437">Uma entidade `Enrollment` é associada a uma entidade `Student`, de modo que a propriedade possa armazenar apenas uma única entidade `Student` (ao contrário da propriedade de navegação `Student.Enrollments` que você viu anteriormente, que pode armazenar várias entidades `Enrollment`).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="e7e3d-438">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e7e3d-439">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e7e3d-440">O Entity Framework interpreta uma propriedade como uma propriedade de chave estrangeira se ela é nomeada `<navigation property name><primary key property name>` (por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e7e3d-441">As propriedades de chave estrangeira também podem ser nomeadas apenas `<primary key property name>` (por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e7e3d-442">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="e7e3d-442">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="e7e3d-444">Na pasta *Models* , crie *Course.cs* e substitua o código existente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-445">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e7e3d-446">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e7e3d-447">Falaremos mais sobre o atributo `DatabaseGenerated` em um [tutorial posterior](complex-data-model.md) desta série.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="e7e3d-448">Basicamente, esse atributo permite que você insira a chave primária do curso, em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="e7e3d-449">Criar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-449">Create the database context</span></span>

<span data-ttu-id="e7e3d-450">A classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="e7e3d-451">Você cria essa classe derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="e7e3d-452">No código, especifique quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="e7e3d-453">Também personalize o comportamento específico do Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="e7e3d-454">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e7e3d-455">Na pasta do projeto, crie uma pasta chamada *Dados*.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="e7e3d-456">Na pasta *Dados* , crie um novo arquivo de classe chamado *SchoolContext.cs* e substitua o código de modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-457">Esse código cria uma propriedade `DbSet` para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="e7e3d-458">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e7e3d-459">Você pode omitir as instruções `DbSet<Enrollment>` e `DbSet<Course>` e elas funcionarão da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="e7e3d-460">O Entity Framework inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="e7e3d-461">Quando o banco de dados é criado, o EF cria tabelas que têm nomes iguais aos nomes de propriedade `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="e7e3d-462">Em geral, os nomes de propriedade de coleções são plurais (Alunos em vez de Aluno), mas os desenvolvedores não concordam sobre se os nomes de tabela devem ser pluralizados ou não.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="e7e3d-463">Para esses tutoriais, você substituirá o comportamento padrão especificando nomes singulares de tabela no DbContext.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="e7e3d-464">Para fazer isso, adicione o código realçado a seguir após a última propriedade DbSet.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="e7e3d-465">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="e7e3d-466">Registrar o SchoolContext</span><span class="sxs-lookup"><span data-stu-id="e7e3d-466">Register the SchoolContext</span></span>

<span data-ttu-id="e7e3d-467">O ASP.NET Core implementa a [injeção de dependência](../../fundamentals/dependency-injection.md) por padrão.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="e7e3d-468">Serviços (como o contexto de banco de dados do EF) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e7e3d-469">Os componentes que exigem esses serviços (como controladores MVC) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e7e3d-470">Você verá o código de construtor do controlador que obtém uma instância de contexto mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="e7e3d-471">Para registrar `SchoolContext` como um serviço, abra *Startup.cs* e adicione as linhas realçadas ao método `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="e7e3d-472">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="e7e3d-473">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="e7e3d-474">Adicione instruções `using` aos namespaces `ContosoUniversity.Data` e `Microsoft.EntityFrameworkCore` e, em seguida, compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="e7e3d-475">Abra o *:::no-loc(appsettings.json):::* arquivo e adicione uma cadeia de conexão, conforme mostrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-475">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e7e3d-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e7e3d-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e7e3d-477">A cadeia de conexão especifica um banco de dados LocalDB do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="e7e3d-478">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="e7e3d-479">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e7e3d-480">Por padrão, o LocalDB cria arquivos de banco de dados *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="e7e3d-481">Inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="e7e3d-481">Initialize DB with test data</span></span>

<span data-ttu-id="e7e3d-482">O Entity Framework criará um banco de dados vazio para você.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="e7e3d-483">Nesta seção, você escreve um método que é chamado depois que o banco de dados é criado para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="e7e3d-484">Aqui, você usará o método `EnsureCreated` para criar o banco de dados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="e7e3d-485">Em um [tutorial posterior](migrations.md), você verá como manipular as alterações do modelo usando as Migrações do Code First para alterar o esquema de banco de dados, em vez de remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="e7e3d-486">Na pasta *Data* , crie um novo arquivo de classe chamado *DbInitializer.cs* e substitua o código de modelo pelo código a seguir, que faz com que um banco de dados seja criado, quando necessário, e carrega dados de teste no novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="e7e3d-487">O código verifica se há alunos no banco de dados e, se não há, ele pressupõe que o banco de dados é novo e precisa ser propagado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="e7e3d-488">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="e7e3d-489">Em *Program.cs* , modifique o método `Main` para fazer o seguinte na inicialização do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="e7e3d-490">Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e7e3d-491">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e7e3d-492">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="e7e3d-493">Na primeira vez em que você executar o aplicativo, o banco de dados será criado e propagado com dado de teste.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="e7e3d-494">Sempre que você alterar o modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="e7e3d-495">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-495">Delete the database.</span></span>
 * <span data-ttu-id="e7e3d-496">Atualize o método semente e inicie o novamente com um novo banco de dados da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="e7e3d-497">Nos próximos tutoriais, você verá como modificar o banco de dados quando o modelo de dados for alterado, sem excluí-lo e recriá-lo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="e7e3d-498">Criar um controlador e exibições</span><span class="sxs-lookup"><span data-stu-id="e7e3d-498">Create controller and views</span></span>

<span data-ttu-id="e7e3d-499">Nesta seção, o mecanismo scaffolding no Visual Studio é usado para adicionar um controlador MVC e modos de exibição que usarão o EF para consultar e salvar dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="e7e3d-500">A criação automática de métodos de ação CRUD e exibições é conhecida como scaffolding.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="e7e3d-501">O scaffolding difere da geração de código, em que o código gerado por scaffolding é um ponto de partida que você pode modificar de acordo com seus requisitos, enquanto que normalmente o código gerado não é modificado.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="e7e3d-502">Quando precisar personalizar o código gerado, use classes parciais ou regenere o código quando as coisas mudarem.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="e7e3d-503">Clique com o botão direito do mouse na pasta **Controladores** no **Gerenciador de Soluções** e selecione **Adicionar > Novo Item Gerado por Scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="e7e3d-504">Na caixa de diálogo **Adicionar Scaffolding** :</span><span class="sxs-lookup"><span data-stu-id="e7e3d-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="e7e3d-505">Selecione **Controlador MVC com exibições, usando o Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="e7e3d-506">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-506">Click **Add**.</span></span> <span data-ttu-id="e7e3d-507">A caixa de diálogo **Adicionar controlador MVC com exibições, usando Entity Framework** é exibida: ![ Scaffold Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="e7e3d-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="e7e3d-508">Na **classe Model** , selecione **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="e7e3d-509">Na **Classe de contexto de dados** selecione **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="e7e3d-510">Aceite o **StudentsController** padrão como o nome.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="e7e3d-511">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-511">Click **Add**.</span></span>

<span data-ttu-id="e7e3d-512">O mecanismo de scaffolding do Visual Studio cria um arquivo *StudentsController.cs* e um conjunto de exibições (arquivos *. cshtml* ) que funcionam com o controlador.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="e7e3d-513">Observe que o controlador usa `SchoolContext` como um parâmetro de construtor.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="e7e3d-514">A injeção de dependência do ASP.NET Core é responsável por passar uma instância de `SchoolContext` para o controlador.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="e7e3d-515">Que foi configurado no arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="e7e3d-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="e7e3d-516">O controlador contém um método de ação `Index`, que exibe todos os alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="e7e3d-517">O método obtém uma lista de alunos do conjunto de entidades Students pela leitura da propriedade `Students` da instância de contexto de banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="e7e3d-518">Você aprenderá sobre os elementos de programação assíncrona neste código posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="e7e3d-519">A exibição *Views/Students/Index.cshtml* mostra esta lista em uma tabela:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="e7e3d-520">Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="e7e3d-521">Clique na guia Alunos para ver os dados de teste inserido pelo método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="e7e3d-522">Dependendo da largura da janela do navegador, você verá o link da guia `Students` na parte superior da página ou precisará clicar no ícone de navegação no canto superior direito para ver o link.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Home page estreita da Contoso University](intro/_static/home-page-narrow.png)

![Página Índice de Alunos](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="e7e3d-525">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7e3d-525">View the database</span></span>

<span data-ttu-id="e7e3d-526">Quando você iniciou o aplicativo, o método `DbInitializer.Initialize` chamou `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="e7e3d-527">O EF observou que não havia nenhum banco de dados e, portanto, ele criou um; em seguida, o restante do código do método `Initialize` populou o banco de dados com os dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="e7e3d-528">Use o **SSOX** (Pesquisador de Objetos do SQL Server) para exibir o banco de dados no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="e7e3d-529">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-529">Close the browser.</span></span>

<span data-ttu-id="e7e3d-530">Se a janela do SSOX ainda não estiver aberta, selecione-a no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="e7e3d-531">Em SSOX, clique em **(LocalDB) \MSSQLLocalDB > bancos** de dados e, em seguida, clique na entrada do nome do banco de dados que está na cadeia de conexão no *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="e7e3d-532">Expanda o nó **tabelas** para ver as tabelas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-532">Expand the **Tables** node to see the tables in the database.</span></span>

![Tabelas no SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="e7e3d-534">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas que foram inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabela Aluno no SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="e7e3d-536">Os arquivos de banco de dados *.mdf* e *.ldf* estão na pasta *C:\Users\\\<username>*.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="e7e3d-537">Como você está chamando `EnsureCreated` no método inicializador executado na inicialização do aplicativo, agora você pode fazer uma alteração na classe `Student`, excluir o banco de dados, executar novamente o aplicativo e o banco de dados será recriado automaticamente para que ele corresponda à alteração.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="e7e3d-538">Por exemplo, se você adicionar uma propriedade `EmailAddress` à classe `Student`, verá uma nova coluna `EmailAddress` na tabela recriada.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="e7e3d-539">Convenções</span><span class="sxs-lookup"><span data-stu-id="e7e3d-539">Conventions</span></span>

<span data-ttu-id="e7e3d-540">A quantidade de código feita para que o Entity Framework possa criar um banco de dados completo para você é mínima, devido ao uso de convenções ou de suposições feitas pelo Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="e7e3d-541">Os nomes de propriedades `DbSet` são usadas como nomes de tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="e7e3d-542">Para entidades não referenciadas por uma propriedade `DbSet`, os nomes de classe de entidade são usados como nomes de tabela.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="e7e3d-543">Os nomes de propriedade de entidade são usados para nomes de coluna.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="e7e3d-544">As propriedades de entidade que são nomeadas ID ou classnameID são reconhecidas como propriedades de chave primária.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="e7e3d-545">Uma propriedade será interpretada como uma propriedade de chave estrangeira se ela for nomeada *\<navigation property name>\<primary key property name>* (por exemplo, `StudentID` para a `Student` propriedade de navegação, uma vez que a `Student` chave primária da entidade é `ID` ).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e7e3d-546">As propriedades de chave estrangeira também podem ser nomeadas simplesmente *\<primary key property name>* (por exemplo, `EnrollmentID` uma vez que a `Enrollment` chave primária da entidade é `EnrollmentID` ).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="e7e3d-547">O comportamento convencional pode ser substituído.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="e7e3d-548">Por exemplo, você pode especificar os nomes de tabela de forma explícita, conforme visto anteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="e7e3d-549">Além disso, você pode definir nomes de coluna e qualquer propriedade como a chave primária ou chave estrangeira, como você verá em um [tutorial posterior](complex-data-model.md) desta série.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e7e3d-550">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="e7e3d-550">Asynchronous code</span></span>

<span data-ttu-id="e7e3d-551">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e7e3d-552">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e7e3d-553">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e7e3d-554">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e7e3d-555">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e7e3d-556">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e7e3d-557">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, ao passo que, em situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e7e3d-558">No código a seguir, a palavra-chave `async`, o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e7e3d-559">A palavra-chave `async` instrui o compilador a gerar retornos de chamada para partes do corpo do método e a criar automaticamente o objeto `Task<IActionResult>` que é retornado.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="e7e3d-560">O tipo de retorno `Task<IActionResult>` representa um trabalho em andamento com um resultado do tipo `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="e7e3d-561">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e7e3d-562">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e7e3d-563">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="e7e3d-564">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e7e3d-565">Algumas coisas a serem consideradas quando você estiver escrevendo um código assíncrono que usa o Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="e7e3d-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="e7e3d-566">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="e7e3d-567">Isso inclui, por exemplo, `ToListAsync`, `SingleOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e7e3d-568">Isso não inclui, por exemplo, instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="e7e3d-569">Um contexto do EF não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="e7e3d-570">Quando você chamar qualquer método assíncrono do EF, sempre use a palavra-chave `await`.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="e7e3d-571">Se desejar aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca que você está usando (por exemplo, para paginação) também usam o código assíncrono se eles chamam métodos do Entity Framework que fazem com que consultas sejam enviadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="e7e3d-572">Para obter mais informações sobre a programação assíncrona no .NET, consulte [Visão geral da programação assíncrona](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="e7e3d-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7e3d-573">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e7e3d-573">Next steps</span></span>

<span data-ttu-id="e7e3d-574">Vá para o próximo tutorial para aprender a executar operações CRUD (criar, ler, atualizar e excluir) básicas.</span><span class="sxs-lookup"><span data-stu-id="e7e3d-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e7e3d-575">Implementar a funcionalidade CRUD básica</span><span class="sxs-lookup"><span data-stu-id="e7e3d-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
