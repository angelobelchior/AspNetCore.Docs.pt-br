---
title: ':::no-loc(Razor)::: Páginas com Entity Framework Core no ASP.NET Core-tutorial 1 de 8'
author: rick-anderson
description: 'Mostra como criar um :::no-loc(Razor)::: aplicativo de páginas usando Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: c4b4f2b89be2018857abaafb448f052c3848ec59
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054067"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="29b50-103">:::no-loc(Razor)::: Páginas com Entity Framework Core no ASP.NET Core-tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="29b50-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="29b50-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29b50-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="29b50-105">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="29b50-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="29b50-106">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="29b50-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="29b50-107">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="29b50-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="29b50-108">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="29b50-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="29b50-109">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="29b50-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="29b50-110">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="29b50-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="29b50-111">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="29b50-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="29b50-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="29b50-112">Prerequisites</span></span>

* <span data-ttu-id="29b50-113">Se você for novo em :::no-loc(Razor)::: páginas, consulte a série de tutoriais [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="29b50-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="29b50-116">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-116">Database engines</span></span>

<span data-ttu-id="29b50-117">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="29b50-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="29b50-118">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="29b50-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="29b50-119">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="29b50-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="29b50-120">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="29b50-120">Troubleshooting</span></span>

<span data-ttu-id="29b50-121">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="29b50-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="29b50-122">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="29b50-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="29b50-123">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="29b50-123">The sample app</span></span>

<span data-ttu-id="29b50-124">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="29b50-125">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="29b50-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="29b50-126">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-126">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="29b50-129">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="29b50-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="29b50-130">O foco do tutorial é sobre como usar EF Core com ASP.NET Core, não como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="29b50-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="29b50-131">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="29b50-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-133">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto** .</span><span class="sxs-lookup"><span data-stu-id="29b50-133">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="29b50-134">Selecione **Aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="29b50-134">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="29b50-135">Nomeie o projeto *ContosoUniversity* .</span><span class="sxs-lookup"><span data-stu-id="29b50-135">Name the project *ContosoUniversity* .</span></span> <span data-ttu-id="29b50-136">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="29b50-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="29b50-137">Selecione **.NET Core** e **ASP.NET Core 5,0** nos menus suspensos e, em seguida, selecione **aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="29b50-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-139">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="29b50-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="29b50-140">Execute os seguintes comandos para criar um :::no-loc(Razor)::: projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="29b50-140">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="29b50-141">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="29b50-141">Set up the site style</span></span>

<span data-ttu-id="29b50-142">Copie e cole o código a seguir no arquivo *pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="29b50-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="29b50-143">O arquivo de layout define o cabeçalho, o rodapé e o menu do site.</span><span class="sxs-lookup"><span data-stu-id="29b50-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="29b50-144">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="29b50-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="29b50-145">Cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="29b50-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="29b50-146">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="29b50-146">There are three occurrences.</span></span>
* <span data-ttu-id="29b50-147">As entradas do menu **página inicial** e **privacidade** são excluídas.</span><span class="sxs-lookup"><span data-stu-id="29b50-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="29b50-148">As entradas são adicionadas para **about** , **estudantes** , **cursos** , **instrutores** e **departamentos** .</span><span class="sxs-lookup"><span data-stu-id="29b50-148">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments** .</span></span>

<span data-ttu-id="29b50-149">Em *pages/index. cshtml* , substitua o conteúdo do arquivo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="29b50-149">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="29b50-150">O código anterior substitui o texto sobre ASP.NET Core por texto sobre este aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="29b50-151">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="29b50-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="29b50-152">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-152">The data model</span></span>

<span data-ttu-id="29b50-153">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="29b50-153">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="29b50-155">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="29b50-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="29b50-156">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="29b50-156">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="29b50-158">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="29b50-159">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="29b50-160">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="29b50-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="29b50-161">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="29b50-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="29b50-162">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="29b50-163">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="29b50-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="29b50-164">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="29b50-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="29b50-165">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="29b50-166">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="29b50-167">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="29b50-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="29b50-168">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="29b50-169">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="29b50-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="29b50-170">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="29b50-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="29b50-171">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="29b50-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="29b50-172">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="29b50-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="29b50-173">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="29b50-174">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="29b50-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="29b50-175">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="29b50-175">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="29b50-177">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="29b50-178">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="29b50-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="29b50-179">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="29b50-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="29b50-180">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="29b50-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="29b50-181">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="29b50-182">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="29b50-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="29b50-183">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="29b50-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="29b50-184">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="29b50-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="29b50-185">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="29b50-186">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="29b50-187">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="29b50-188">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="29b50-189">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="29b50-190">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="29b50-191">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="29b50-192">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="29b50-193">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="29b50-193">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="29b50-195">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="29b50-196">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="29b50-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="29b50-197">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="29b50-198">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="29b50-199">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="29b50-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="29b50-200">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="29b50-200">Scaffold Student pages</span></span>

<span data-ttu-id="29b50-201">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="29b50-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="29b50-202">Uma classe de EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="29b50-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="29b50-203">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="29b50-204">Ele deriva da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="29b50-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="29b50-205">:::no-loc(Razor)::: páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-205">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-207">Crie uma pasta *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="29b50-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="29b50-208">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold** .</span><span class="sxs-lookup"><span data-stu-id="29b50-208">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="29b50-209">Na caixa de diálogo **Adicionar novo item do Scaffold** :</span><span class="sxs-lookup"><span data-stu-id="29b50-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="29b50-210">Na guia à esquerda, selecione **instalado > :::no-loc(Razor)::: páginas > comuns**</span><span class="sxs-lookup"><span data-stu-id="29b50-210">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="29b50-211">Selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="29b50-211">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD** .</span></span>
* <span data-ttu-id="29b50-212">Na caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="29b50-212">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="29b50-213">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="29b50-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)** .</span></span>
  * <span data-ttu-id="29b50-214">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="29b50-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="29b50-215">Altere o nome do contexto de dados para terminar em `SchoolContext` em vez de `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="29b50-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="29b50-216">O nome do contexto atualizado: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="29b50-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="29b50-217">Selecione **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="29b50-217">Select **Add** .</span></span>

<span data-ttu-id="29b50-218">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="29b50-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-220">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="29b50-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="29b50-221">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="29b50-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="29b50-222">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="29b50-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="29b50-223">Crie uma pasta *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="29b50-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="29b50-224">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="29b50-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="29b50-225">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="29b50-226">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="29b50-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="29b50-227">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="29b50-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="29b50-228">Se a etapa anterior falhar, compile o projeto e tente a etapa Scaffold novamente.</span><span class="sxs-lookup"><span data-stu-id="29b50-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="29b50-229">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="29b50-229">The scaffolding process:</span></span>

* <span data-ttu-id="29b50-230">Cria :::no-loc(Razor)::: páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="29b50-230">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="29b50-231">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-232">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-233">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-234">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-235">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="29b50-236">Cria *Data/SchoolContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="29b50-236">Creates *Data/SchoolContext.cs* .</span></span>
* <span data-ttu-id="29b50-237">Adiciona o contexto à injeção de dependência em *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="29b50-237">Adds the context to dependency injection in *Startup.cs* .</span></span>
* <span data-ttu-id="29b50-238">Adiciona uma cadeia de conexão de banco de dados a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="29b50-238">Adds a database connection string to *:::no-loc(appsettings.json):::* .</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="29b50-239">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-239">Database connection string</span></span>

<span data-ttu-id="29b50-240">A ferramenta scaffolding gera uma cadeia de conexão no *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="29b50-240">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-242">A cadeia de conexão especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="29b50-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="29b50-243">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="29b50-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="29b50-244">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-246">Encurte a cadeia de conexão do SQLite para *cu. db* :</span><span class="sxs-lookup"><span data-stu-id="29b50-246">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="29b50-247">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-247">Update the database context class</span></span>

<span data-ttu-id="29b50-248">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="29b50-249">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="29b50-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="29b50-250">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="29b50-251">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="29b50-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="29b50-252">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="29b50-253">O código anterior muda do singular `DbSet<Student> Student` para o plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="29b50-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="29b50-254">Para fazer com que o :::no-loc(Razor)::: código das páginas corresponda ao novo `DBSet` nome, faça uma alteração global de: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="29b50-254">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="29b50-255">Para: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="29b50-255">to: `_context.Students.`</span></span>

<span data-ttu-id="29b50-256">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="29b50-256">There are 8 occurrences.</span></span>

<span data-ttu-id="29b50-257">Como um conjunto de entidades contém várias entidades, muitos desenvolvedores preferem que os `DBSet` nomes de propriedade devem ser plural.</span><span class="sxs-lookup"><span data-stu-id="29b50-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="29b50-258">O código realçado:</span><span class="sxs-lookup"><span data-stu-id="29b50-258">The highlighted code:</span></span>

* <span data-ttu-id="29b50-259">Cria uma [propriedade \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="29b50-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="29b50-260">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="29b50-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="29b50-261">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="29b50-262">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="29b50-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="29b50-263">Chama <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="29b50-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="29b50-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="29b50-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="29b50-265">É chamado quando foi `SchoolContext` inicializado, mas antes de o modelo ser bloqueado e usado para inicializar o contexto.</span><span class="sxs-lookup"><span data-stu-id="29b50-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="29b50-266">É necessário porque, posteriormente, no tutorial `Student` , a entidade terá referências a outras entidades.</span><span class="sxs-lookup"><span data-stu-id="29b50-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="29b50-267">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="29b50-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="29b50-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="29b50-268">Startup.cs</span></span>

<span data-ttu-id="29b50-269">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="29b50-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="29b50-270">Serviços como o `SchoolContext` são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="29b50-271">Os componentes que exigem esses serviços, como :::no-loc(Razor)::: páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="29b50-271">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="29b50-272">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="29b50-273">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="29b50-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-275">As linhas realçadas a seguir foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="29b50-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-277">Verifique o código adicionado pelas chamadas scaffolder `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="29b50-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="29b50-278">Consulte [usar o SQLite para desenvolvimento, SQL Server para produção](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) para obter informações sobre como usar um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="29b50-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="29b50-279">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="29b50-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="29b50-280">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="29b50-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="29b50-281">Adicionar o filtro de exceção de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-281">Add the database exception filter</span></span>

<span data-ttu-id="29b50-282">Adicione `AddDatabaseDeveloperPageExceptionFilter` ao `ConfigureServices` conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="29b50-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="29b50-284">Adicione o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="29b50-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="29b50-285">No PMC, digite o seguinte comando para adicionar o pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="29b50-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="29b50-287">O `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacote NuGet fornece ASP.NET Core middleware para Entity Framework Core páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="29b50-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="29b50-288">Esse middleware ajuda a detectar e diagnosticar erros com Entity Framework Core migrações.</span><span class="sxs-lookup"><span data-stu-id="29b50-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="29b50-289">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-289">Create the database</span></span>

<span data-ttu-id="29b50-290">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="29b50-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="29b50-291">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="29b50-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="29b50-292">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="29b50-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="29b50-293">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="29b50-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="29b50-294">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-294">Delete the database.</span></span> <span data-ttu-id="29b50-295">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="29b50-295">Any existing data is lost.</span></span>
* <span data-ttu-id="29b50-296">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-296">Change the data model.</span></span> <span data-ttu-id="29b50-297">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="29b50-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="29b50-298">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-298">Run the app.</span></span>
* <span data-ttu-id="29b50-299">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="29b50-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="29b50-300">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="29b50-301">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="29b50-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="29b50-302">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="29b50-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="29b50-303">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="29b50-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="29b50-304">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="29b50-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="29b50-305">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="29b50-305">Test the app</span></span>

* <span data-ttu-id="29b50-306">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-306">Run the app.</span></span>
* <span data-ttu-id="29b50-307">Selecione o link **Alunos** e **Criar Novo** .</span><span class="sxs-lookup"><span data-stu-id="29b50-307">Select the **Students** link and then **Create New** .</span></span>
* <span data-ttu-id="29b50-308">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="29b50-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="29b50-309">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-309">Seed the database</span></span>

<span data-ttu-id="29b50-310">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="29b50-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="29b50-311">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="29b50-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="29b50-312">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="29b50-313">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="29b50-314">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="29b50-315">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="29b50-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="29b50-316">Em *Program.cs* , substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="29b50-316">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-318">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="29b50-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="29b50-319">Responda com `Y` para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-321">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db* .</span><span class="sxs-lookup"><span data-stu-id="29b50-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="29b50-322">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-322">Restart the app.</span></span>
* <span data-ttu-id="29b50-323">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="29b50-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="29b50-324">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-326">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29b50-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="29b50-327">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="29b50-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span></span> <span data-ttu-id="29b50-328">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="29b50-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="29b50-329">Expanda o nó **Tabelas** .</span><span class="sxs-lookup"><span data-stu-id="29b50-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="29b50-330">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="29b50-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="29b50-331">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-333">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="29b50-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="29b50-334">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="29b50-335">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="29b50-335">Asynchronous code</span></span>

<span data-ttu-id="29b50-336">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="29b50-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="29b50-337">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="29b50-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="29b50-338">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="29b50-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="29b50-339">Com o código síncrono, muitos threads podem ser ligados enquanto não estão trabalhando porque estão aguardando a conclusão de e/s.</span><span class="sxs-lookup"><span data-stu-id="29b50-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="29b50-340">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="29b50-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="29b50-341">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="29b50-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="29b50-342">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="29b50-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="29b50-343">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="29b50-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="29b50-344">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="29b50-345">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="29b50-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="29b50-346">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="29b50-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="29b50-347">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="29b50-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="29b50-348">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="29b50-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="29b50-349">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="29b50-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="29b50-350">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="29b50-351">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="29b50-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="29b50-352">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="29b50-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="29b50-353">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="29b50-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="29b50-354">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="29b50-355">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="29b50-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="29b50-356">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="29b50-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="29b50-357">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="29b50-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="29b50-358">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="29b50-359">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="29b50-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="29b50-360">Considerações sobre o desempenho</span><span class="sxs-lookup"><span data-stu-id="29b50-360">Performance considerations</span></span>

<span data-ttu-id="29b50-361">Em geral, uma página da Web não deve carregar um número arbitrário de linhas.</span><span class="sxs-lookup"><span data-stu-id="29b50-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="29b50-362">Uma consulta deve usar a paginação ou uma abordagem de limitação.</span><span class="sxs-lookup"><span data-stu-id="29b50-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="29b50-363">Por exemplo, a consulta anterior poderia usar `Take` para limitar as linhas retornadas:</span><span class="sxs-lookup"><span data-stu-id="29b50-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="29b50-364">Enumerar uma tabela grande em uma exibição pode retornar uma resposta HTTP 200 parcialmente construída se uma exceção de banco de dados ocorrer parcialmente por meio da enumeração.</span><span class="sxs-lookup"><span data-stu-id="29b50-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="29b50-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> o padrão é 1024.</span><span class="sxs-lookup"><span data-stu-id="29b50-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="29b50-366">Os seguintes conjuntos de código `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="29b50-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="29b50-367">A paginação será abordada posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="29b50-368">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="29b50-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="29b50-369">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="29b50-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="29b50-370">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="29b50-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="29b50-371">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="29b50-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="29b50-372">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="29b50-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="29b50-373">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="29b50-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="29b50-374">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="29b50-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="29b50-375">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="29b50-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="29b50-376">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="29b50-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="29b50-377">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="29b50-377">Prerequisites</span></span>

* <span data-ttu-id="29b50-378">Se você for novo em :::no-loc(Razor)::: páginas, consulte a série de tutoriais [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="29b50-378">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="29b50-381">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-381">Database engines</span></span>

<span data-ttu-id="29b50-382">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="29b50-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="29b50-383">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="29b50-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="29b50-384">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="29b50-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="29b50-385">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="29b50-385">Troubleshooting</span></span>

<span data-ttu-id="29b50-386">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="29b50-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="29b50-387">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="29b50-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="29b50-388">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="29b50-388">The sample app</span></span>

<span data-ttu-id="29b50-389">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="29b50-390">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="29b50-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="29b50-391">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-391">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="29b50-394">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="29b50-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="29b50-395">O foco do tutorial está em como usar o EF Core, não em como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="29b50-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="29b50-396">Siga o link na parte superior da página para obter o código-fonte do projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="29b50-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="29b50-397">A pasta *cu30* tem o código para a versão ASP.NET Core 3.0 do tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="29b50-398">Os arquivos que refletem o estado do código para os tutoriais 1-7 podem ser encontrados na pasta *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="29b50-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-400">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="29b50-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="29b50-401">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-401">Build the project.</span></span>
* <span data-ttu-id="29b50-402">No PMC (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="29b50-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="29b50-403">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-405">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="29b50-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="29b50-406">Exclua *ContosoUniversity.csproj* e altere o nome de *ContosoUniversitySQLite.csproj* para *ContosoUniversity.csproj* .</span><span class="sxs-lookup"><span data-stu-id="29b50-406">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj* .</span></span>
* <span data-ttu-id="29b50-407">No *Program.cs* , comente `#define Startup` que `StartupSQLite` é usado.</span><span class="sxs-lookup"><span data-stu-id="29b50-407">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="29b50-408">Exclua *appSettings.json* e altere o nome de *appSettingsSQLite.json* para *appSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="29b50-408">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json* .</span></span>
* <span data-ttu-id="29b50-409">Exclua a pasta *Migrations* e altere o nome de *MigrationsSQL* para *Migrations* .</span><span class="sxs-lookup"><span data-stu-id="29b50-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations* .</span></span>
* <span data-ttu-id="29b50-410">Faça uma pesquisa global para `#if SQLiteVersion` e remova `#if SQLiteVersion` e a `#endif` instrução associada.</span><span class="sxs-lookup"><span data-stu-id="29b50-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="29b50-411">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-411">Build the project.</span></span>
* <span data-ttu-id="29b50-412">Em um prompt de comando na pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="29b50-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="29b50-413">Em sua ferramenta do SQLite, execute a seguinte instrução SQL:</span><span class="sxs-lookup"><span data-stu-id="29b50-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="29b50-414">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="29b50-415">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="29b50-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-417">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto** .</span><span class="sxs-lookup"><span data-stu-id="29b50-417">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="29b50-418">Selecione **Aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="29b50-418">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="29b50-419">Nomeie o projeto *ContosoUniversity* .</span><span class="sxs-lookup"><span data-stu-id="29b50-419">Name the project *ContosoUniversity* .</span></span> <span data-ttu-id="29b50-420">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="29b50-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="29b50-421">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa e, em seguida, selecione **Aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="29b50-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-423">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="29b50-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="29b50-424">Execute os seguintes comandos para criar um :::no-loc(Razor)::: projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="29b50-424">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="29b50-425">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="29b50-425">Set up the site style</span></span>

<span data-ttu-id="29b50-426">Configure o cabeçalho, o rodapé e o menu do site atualizando *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="29b50-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="29b50-427">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="29b50-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="29b50-428">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="29b50-428">There are three occurrences.</span></span>

* <span data-ttu-id="29b50-429">Exclua as entradas de menu **Início** e **Privacidade** , então adicione as entradas para **Sobre** , **Alunos** , **Cursos** , **Instrutores** e **Departamentos** .</span><span class="sxs-lookup"><span data-stu-id="29b50-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** .</span></span>

<span data-ttu-id="29b50-430">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="29b50-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="29b50-431">Em *Pages/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET Core pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="29b50-431">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="29b50-432">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="29b50-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="29b50-433">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-433">The data model</span></span>

<span data-ttu-id="29b50-434">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="29b50-434">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="29b50-436">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="29b50-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="29b50-437">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="29b50-437">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="29b50-439">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="29b50-440">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="29b50-441">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="29b50-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="29b50-442">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="29b50-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="29b50-443">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="29b50-444">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="29b50-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="29b50-445">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="29b50-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="29b50-446">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="29b50-447">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="29b50-448">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="29b50-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="29b50-449">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="29b50-450">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="29b50-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="29b50-451">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="29b50-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="29b50-452">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="29b50-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="29b50-453">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="29b50-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="29b50-454">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="29b50-455">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="29b50-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="29b50-456">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="29b50-456">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="29b50-458">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="29b50-459">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="29b50-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="29b50-460">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="29b50-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="29b50-461">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="29b50-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="29b50-462">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="29b50-463">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="29b50-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="29b50-464">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="29b50-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="29b50-465">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="29b50-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="29b50-466">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="29b50-467">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="29b50-468">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="29b50-469">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="29b50-470">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="29b50-471">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="29b50-472">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="29b50-473">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="29b50-474">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="29b50-474">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="29b50-476">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="29b50-477">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="29b50-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="29b50-478">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="29b50-479">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="29b50-480">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="29b50-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="29b50-481">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="29b50-481">Scaffold Student pages</span></span>

<span data-ttu-id="29b50-482">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="29b50-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="29b50-483">Uma classe *contexto* do EF Core.</span><span class="sxs-lookup"><span data-stu-id="29b50-483">An EF Core *context* class.</span></span> <span data-ttu-id="29b50-484">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="29b50-485">Ele deriva da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="29b50-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="29b50-486">:::no-loc(Razor)::: páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-486">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-488">Crie uma pasta *Alunos* na pasta *Páginas* .</span><span class="sxs-lookup"><span data-stu-id="29b50-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="29b50-489">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold** .</span><span class="sxs-lookup"><span data-stu-id="29b50-489">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="29b50-490">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="29b50-490">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD** .</span></span>
* <span data-ttu-id="29b50-491">Na caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="29b50-491">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="29b50-492">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="29b50-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)** .</span></span>
  * <span data-ttu-id="29b50-493">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="29b50-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="29b50-494">Altere o nome do contexto de dados de *ContosoUniversity.Models.ContosoUniversityContext* para *ContosoUniversity.Data.SchoolContext* .</span><span class="sxs-lookup"><span data-stu-id="29b50-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext* .</span></span>
  * <span data-ttu-id="29b50-495">Selecione **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="29b50-495">Select **Add** .</span></span>

<span data-ttu-id="29b50-496">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="29b50-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-498">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="29b50-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="29b50-499">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="29b50-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="29b50-500">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="29b50-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="29b50-501">Crie uma pasta *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="29b50-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="29b50-502">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="29b50-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="29b50-503">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="29b50-504">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="29b50-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="29b50-505">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="29b50-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="29b50-506">Se você tiver um problema com a etapa anterior, compile o projeto e repita a etapa de scaffold.</span><span class="sxs-lookup"><span data-stu-id="29b50-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="29b50-507">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="29b50-507">The scaffolding process:</span></span>

* <span data-ttu-id="29b50-508">Cria :::no-loc(Razor)::: páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="29b50-508">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="29b50-509">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-510">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-511">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-512">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="29b50-513">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="29b50-514">Cria *Data/SchoolContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="29b50-514">Creates *Data/SchoolContext.cs* .</span></span>
* <span data-ttu-id="29b50-515">Adiciona o contexto à injeção de dependência em *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="29b50-515">Adds the context to dependency injection in *Startup.cs* .</span></span>
* <span data-ttu-id="29b50-516">Adiciona uma cadeia de conexão de banco de dados a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="29b50-516">Adds a database connection string to *:::no-loc(appsettings.json):::* .</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="29b50-517">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-519">O *:::no-loc(appsettings.json):::* arquivo especifica a cadeia de conexão [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="29b50-519">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="29b50-520">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="29b50-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="29b50-521">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-523">Altere a cadeia de conexão para apontar para um arquivo de banco de dados SQLite chamado *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="29b50-523">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="29b50-524">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-524">Update the database context class</span></span>

<span data-ttu-id="29b50-525">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="29b50-526">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="29b50-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="29b50-527">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="29b50-528">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="29b50-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="29b50-529">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="29b50-530">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="29b50-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="29b50-531">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="29b50-531">In EF Core terminology:</span></span>

* <span data-ttu-id="29b50-532">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="29b50-533">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="29b50-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="29b50-534">Como um conjunto de entidades contém várias entidades, as propriedades DBSet devem ser nomes no plural.</span><span class="sxs-lookup"><span data-stu-id="29b50-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="29b50-535">Como a ferramenta scaffolding criou um`Student` DBSet, essa etapa o altera para o `Students` no plural.</span><span class="sxs-lookup"><span data-stu-id="29b50-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="29b50-536">Para fazer com que o :::no-loc(Razor)::: código das páginas corresponda ao novo nome de DBSet, faça uma alteração global em todo o projeto de `_context.Student` para `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="29b50-536">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="29b50-537">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="29b50-537">There are 8 occurrences.</span></span>

<span data-ttu-id="29b50-538">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="29b50-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="29b50-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="29b50-539">Startup.cs</span></span>

<span data-ttu-id="29b50-540">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="29b50-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="29b50-541">Serviços (como o contexto de banco de dados do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="29b50-542">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="29b50-542">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="29b50-543">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="29b50-544">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="29b50-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-546">Em `ConfigureServices`, as linhas destacadas foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="29b50-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-548">Em `ConfigureServices`, verifique se o código adicionado pelo scaffolder chama `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="29b50-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="29b50-549">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="29b50-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="29b50-550">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="29b50-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="29b50-551">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-551">Create the database</span></span>

<span data-ttu-id="29b50-552">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="29b50-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="29b50-553">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="29b50-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="29b50-554">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="29b50-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="29b50-555">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="29b50-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="29b50-556">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-556">Delete the database.</span></span> <span data-ttu-id="29b50-557">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="29b50-557">Any existing data is lost.</span></span>
* <span data-ttu-id="29b50-558">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-558">Change the data model.</span></span> <span data-ttu-id="29b50-559">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="29b50-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="29b50-560">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-560">Run the app.</span></span>
* <span data-ttu-id="29b50-561">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="29b50-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="29b50-562">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="29b50-563">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="29b50-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="29b50-564">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="29b50-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="29b50-565">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="29b50-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="29b50-566">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="29b50-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="29b50-567">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="29b50-567">Test the app</span></span>

* <span data-ttu-id="29b50-568">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-568">Run the app.</span></span>
* <span data-ttu-id="29b50-569">Selecione o link **Alunos** e **Criar Novo** .</span><span class="sxs-lookup"><span data-stu-id="29b50-569">Select the **Students** link and then **Create New** .</span></span>
* <span data-ttu-id="29b50-570">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="29b50-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="29b50-571">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-571">Seed the database</span></span>

<span data-ttu-id="29b50-572">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="29b50-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="29b50-573">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="29b50-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="29b50-574">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="29b50-575">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="29b50-576">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="29b50-577">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="29b50-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="29b50-578">Em *Program.cs* , substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="29b50-578">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-580">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="29b50-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-582">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db* .</span><span class="sxs-lookup"><span data-stu-id="29b50-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="29b50-583">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-583">Restart the app.</span></span>

* <span data-ttu-id="29b50-584">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="29b50-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="29b50-585">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-587">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29b50-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="29b50-588">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="29b50-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span></span> <span data-ttu-id="29b50-589">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="29b50-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="29b50-590">Expanda o nó **Tabelas** .</span><span class="sxs-lookup"><span data-stu-id="29b50-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="29b50-591">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="29b50-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="29b50-592">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-594">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="29b50-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="29b50-595">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="29b50-596">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="29b50-596">Asynchronous code</span></span>

<span data-ttu-id="29b50-597">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="29b50-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="29b50-598">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="29b50-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="29b50-599">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="29b50-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="29b50-600">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="29b50-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="29b50-601">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="29b50-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="29b50-602">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="29b50-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="29b50-603">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="29b50-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="29b50-604">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="29b50-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="29b50-605">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="29b50-606">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="29b50-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="29b50-607">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="29b50-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="29b50-608">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="29b50-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="29b50-609">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="29b50-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="29b50-610">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="29b50-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="29b50-611">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="29b50-612">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="29b50-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="29b50-613">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="29b50-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="29b50-614">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="29b50-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="29b50-615">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="29b50-616">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="29b50-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="29b50-617">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="29b50-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="29b50-618">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="29b50-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="29b50-619">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="29b50-620">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="29b50-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="29b50-621">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="29b50-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="29b50-622">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="29b50-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29b50-623">O aplicativo Web de exemplo Contoso University demonstra como criar um :::no-loc(Razor)::: aplicativo de páginas ASP.NET Core usando Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="29b50-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="29b50-624">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="29b50-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="29b50-625">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="29b50-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="29b50-626">Esta página é a primeira de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="29b50-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="29b50-627">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="29b50-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="29b50-628">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="29b50-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="29b50-629">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="29b50-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="29b50-632">Familiaridade com [ :::no-loc(Razor)::: páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="29b50-632">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="29b50-633">Os novos programadores devem concluir a [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta série.</span><span class="sxs-lookup"><span data-stu-id="29b50-633">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="29b50-634">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="29b50-634">Troubleshooting</span></span>

<span data-ttu-id="29b50-635">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="29b50-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="29b50-636">Uma boa maneira de obter ajuda é postando uma pergunta no [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="29b50-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="29b50-637">O aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="29b50-637">The Contoso University web app</span></span>

<span data-ttu-id="29b50-638">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="29b50-639">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="29b50-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="29b50-640">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-640">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

<span data-ttu-id="29b50-643">O estilo de interface do usuário deste site é próximo ao que é gerado pelos modelos internos.</span><span class="sxs-lookup"><span data-stu-id="29b50-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="29b50-644">O foco do tutorial está em EF Core com :::no-loc(Razor)::: páginas, não na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="29b50-644">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="29b50-645">Criar o :::no-loc(Razor)::: aplicativo Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="29b50-645">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-647">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto** .</span><span class="sxs-lookup"><span data-stu-id="29b50-647">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="29b50-648">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29b50-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="29b50-649">Nomeie o projeto **ContosoUniversity** .</span><span class="sxs-lookup"><span data-stu-id="29b50-649">Name the project **ContosoUniversity** .</span></span> <span data-ttu-id="29b50-650">É importante nomear o projeto *ContosoUniversity* para que os namespaces sejam correspondentes quando o código for copiado/colado.</span><span class="sxs-lookup"><span data-stu-id="29b50-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="29b50-651">Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="29b50-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application** .</span></span>

<span data-ttu-id="29b50-652">Para imagens das etapas anteriores, consulte [criar um :::no-loc(Razor)::: aplicativo Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="29b50-652">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="29b50-653">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="29b50-655">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="29b50-655">Set up the site style</span></span>

<span data-ttu-id="29b50-656">Algumas alterações configuram o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="29b50-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="29b50-657">Atualize *Pages/Shared/_Layout.cshtml* com as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="29b50-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="29b50-658">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="29b50-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="29b50-659">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="29b50-659">There are three occurrences.</span></span>

* <span data-ttu-id="29b50-660">Adicione entradas de menu para **Alunos** , **Cursos** , **Instrutores** e **Departamentos** e exclua a entrada de menu **Contato** .</span><span class="sxs-lookup"><span data-stu-id="29b50-660">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="29b50-661">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="29b50-661">The changes are highlighted.</span></span> <span data-ttu-id="29b50-662">(Toda a marcação *não* é exibida.)</span><span class="sxs-lookup"><span data-stu-id="29b50-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="29b50-663">Em *Pages/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="29b50-663">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="29b50-664">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="29b50-664">Create the data model</span></span>

<span data-ttu-id="29b50-665">Crie classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="29b50-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="29b50-666">Comece com as três seguintes entidades:</span><span class="sxs-lookup"><span data-stu-id="29b50-666">Start with the following three entities:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="29b50-668">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="29b50-669">Há uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="29b50-670">Um aluno pode se registrar em qualquer quantidade de cursos.</span><span class="sxs-lookup"><span data-stu-id="29b50-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="29b50-671">Um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="29b50-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="29b50-672">Nas seções a seguir, é criada uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="29b50-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="29b50-673">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="29b50-673">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="29b50-675">Crie uma pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="29b50-675">Create a *Models* folder.</span></span> <span data-ttu-id="29b50-676">Na pasta *Models* , crie um arquivo de classe chamado *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="29b50-677">A propriedade `ID` se torna a coluna de chave primária da tabela de BD (banco de dados) que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="29b50-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="29b50-678">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="29b50-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="29b50-679">Em `classnameID`, `classname` é o nome da classe.</span><span class="sxs-lookup"><span data-stu-id="29b50-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="29b50-680">A chave primária alternativa reconhecida automaticamente é `StudentID` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="29b50-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="29b50-681">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="29b50-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="29b50-682">As propriedades de navegação vinculam-se a outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="29b50-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="29b50-683">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazena todas as entidades `Enrollment` relacionadas a essa `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="29b50-684">Por exemplo, se uma linha Aluno no BD tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="29b50-685">Uma linha `Enrollment` relacionada é uma linha que contém o valor de chave primária do aluno na coluna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="29b50-686">Por exemplo, suponha que o aluno com ID=1 tenha duas linhas na tabela `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="29b50-687">A tabela `Enrollment` tem duas linhas com `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="29b50-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="29b50-688">`StudentID` é uma chave estrangeira na tabela `Enrollment` que especifica o aluno na tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="29b50-689">Se uma propriedade de navegação puder armazenar várias entidades, a propriedade de navegação deverá ser um tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="29b50-690">`ICollection<T>` pode ser especificado ou um tipo como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="29b50-691">Quando `ICollection<T>` é usado, o EF Core cria uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="29b50-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="29b50-692">As propriedades de navegação que armazenam várias entidades são provenientes de relações muitos para muitos e um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="29b50-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="29b50-693">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="29b50-693">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="29b50-695">Na pasta *Models* , crie *Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="29b50-696">A propriedade `EnrollmentID` é a chave primária.</span><span class="sxs-lookup"><span data-stu-id="29b50-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="29b50-697">Essa entidade usa o padrão `classnameID` em vez de `ID` como a entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="29b50-698">Normalmente, os desenvolvedores escolhem um padrão e o usam em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="29b50-699">Em um tutorial posterior, o uso de uma ID sem nome de classe é mostrado para facilitar a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="29b50-700">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="29b50-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="29b50-701">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="29b50-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="29b50-702">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="29b50-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="29b50-703">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="29b50-704">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="29b50-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="29b50-705">A entidade `Student` é distinta da propriedade de navegação `Student.Enrollments`, que contém várias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="29b50-706">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="29b50-707">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="29b50-708">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="29b50-709">Por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="29b50-710">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="29b50-711">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="29b50-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="29b50-712">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="29b50-712">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="29b50-714">Na pasta *Models* , crie *Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="29b50-715">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="29b50-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="29b50-716">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="29b50-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="29b50-717">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="29b50-718">Gere um modelo de aluno por scaffold</span><span class="sxs-lookup"><span data-stu-id="29b50-718">Scaffold the student model</span></span>

<span data-ttu-id="29b50-719">Nesta seção, é feito o scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="29b50-720">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="29b50-721">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="29b50-721">Build the project.</span></span>
* <span data-ttu-id="29b50-722">Crie a pasta *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="29b50-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29b50-724">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Pages/Students* > **Adicionar** > **Novo Item com Scaffold** .</span><span class="sxs-lookup"><span data-stu-id="29b50-724">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="29b50-725">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="29b50-725">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD** .</span></span>

<span data-ttu-id="29b50-726">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="29b50-726">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="29b50-727">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="29b50-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)** .</span></span>
* <span data-ttu-id="29b50-728">Na linha **Classe de contexto de dados** , selecione o sinal de (mais) **+** e altere o nome gerado para **ContosoUniversity.Models.SchoolContext** .</span><span class="sxs-lookup"><span data-stu-id="29b50-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext** .</span></span>
* <span data-ttu-id="29b50-729">Na lista suspensa **Classe de contexto de dados** , selecione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="29b50-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="29b50-730">Selecione **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="29b50-730">Select **Add** .</span></span>

![Caixa de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="29b50-732">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se tiver problemas na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="29b50-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29b50-734">Execute os comandos a seguir para fazer scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="29b50-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="29b50-735">O processo de scaffold criou e alterou os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="29b50-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="29b50-736">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="29b50-736">Files created</span></span>

* <span data-ttu-id="29b50-737">*Pages/Students* Criar, Excluir, Detalhes, Editar, Índice.</span><span class="sxs-lookup"><span data-stu-id="29b50-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="29b50-738">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="29b50-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="29b50-739">Atualizações de arquivo</span><span class="sxs-lookup"><span data-stu-id="29b50-739">File updates</span></span>

* <span data-ttu-id="29b50-740">*Startup.cs* : alterações a esse arquivo serão detalhadas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="29b50-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="29b50-741">*:::no-loc(appsettings.json):::* : A cadeia de conexão usada para se conectar a um banco de dados local é adicionada.</span><span class="sxs-lookup"><span data-stu-id="29b50-741">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="29b50-742">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="29b50-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="29b50-743">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="29b50-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="29b50-744">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="29b50-745">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="29b50-745">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="29b50-746">O código de construtor que obtém uma instância de contexto do BD é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="29b50-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="29b50-747">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="29b50-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="29b50-748">Examine o método `ConfigureServices` em *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="29b50-748">Examine the `ConfigureServices` method in *Startup.cs* .</span></span> <span data-ttu-id="29b50-749">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="29b50-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="29b50-750">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="29b50-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="29b50-751">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="29b50-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="29b50-752">Atualizar o principal</span><span class="sxs-lookup"><span data-stu-id="29b50-752">Update main</span></span>

<span data-ttu-id="29b50-753">Em *Program.cs* , modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="29b50-753">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="29b50-754">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="29b50-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="29b50-755">Chame o [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="29b50-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="29b50-756">Descarte o contexto quando o método `EnsureCreated` for concluído.</span><span class="sxs-lookup"><span data-stu-id="29b50-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="29b50-757">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="29b50-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="29b50-758">`EnsureCreated` garante que o banco de dados do contexto exista.</span><span class="sxs-lookup"><span data-stu-id="29b50-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="29b50-759">Se ele existir, nenhuma ação será realizada.</span><span class="sxs-lookup"><span data-stu-id="29b50-759">If it exists, no action is taken.</span></span> <span data-ttu-id="29b50-760">Se ele não existir, o banco de dados e todos os seus esquemas serão criados.</span><span class="sxs-lookup"><span data-stu-id="29b50-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="29b50-761">`EnsureCreated` não usa migrações para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="29b50-762">Um banco de dados criado com `EnsureCreated` não pode ser atualizado posteriormente usando migrações.</span><span class="sxs-lookup"><span data-stu-id="29b50-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="29b50-763">`EnsureCreated` é chamado na inicialização do aplicativo, que permite que o seguinte fluxo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="29b50-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="29b50-764">Exclua o BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-764">Delete the DB.</span></span>
* <span data-ttu-id="29b50-765">Altere o esquema de BD (por exemplo, adicione um campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="29b50-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="29b50-766">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29b50-766">Run the app.</span></span>
* <span data-ttu-id="29b50-767">`EnsureCreated` cria um BD com a coluna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="29b50-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="29b50-768">`EnsureCreated` é conveniente no início do desenvolvimento quando o esquema está evoluindo rapidamente.</span><span class="sxs-lookup"><span data-stu-id="29b50-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="29b50-769">Mais tarde, no tutorial do banco de dados, é excluído e as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="29b50-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="29b50-770">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="29b50-770">Test the app</span></span>

<span data-ttu-id="29b50-771">Execute o aplicativo e aceite a :::no-loc(cookie)::: política.</span><span class="sxs-lookup"><span data-stu-id="29b50-771">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="29b50-772">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="29b50-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="29b50-773">Você pode ler sobre a :::no-loc(cookie)::: política no [suporte de regulamento geral sobre a proteção de dados da UE (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="29b50-773">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="29b50-774">Selecione o link **Alunos** e **Criar Novo** .</span><span class="sxs-lookup"><span data-stu-id="29b50-774">Select the **Students** link and then **Create New** .</span></span>
* <span data-ttu-id="29b50-775">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="29b50-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="29b50-776">Examine o contexto de BD SchoolContext</span><span class="sxs-lookup"><span data-stu-id="29b50-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="29b50-777">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="29b50-778">O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="29b50-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="29b50-779">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="29b50-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="29b50-780">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="29b50-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="29b50-781">Atualize *SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="29b50-782">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="29b50-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="29b50-783">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="29b50-783">In EF Core terminology:</span></span>

* <span data-ttu-id="29b50-784">Um conjunto de entidades normalmente corresponde a uma tabela de BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="29b50-785">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="29b50-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="29b50-786">`DbSet<Enrollment>` e `DbSet<Course>` podem ser omitidos.</span><span class="sxs-lookup"><span data-stu-id="29b50-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="29b50-787">O EF Core inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="29b50-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="29b50-788">Para este tutorial, mantenha `DbSet<Enrollment>` e `DbSet<Course>` no `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="29b50-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="29b50-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="29b50-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="29b50-790">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="29b50-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="29b50-791">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="29b50-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="29b50-792">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="29b50-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="29b50-793">Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="29b50-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="29b50-794">Adicionar um código para inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="29b50-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="29b50-795">O EF Core cria um BD vazio.</span><span class="sxs-lookup"><span data-stu-id="29b50-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="29b50-796">Nesta seção, um método `Initialize` é escrito para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="29b50-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="29b50-797">Na pasta *Dados* , crie um novo arquivo de classe chamado *DbInitializer.cs* e adicione o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="29b50-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="29b50-798">Observação: o código anterior usa `Models` para o namespace ( `namespace ContosoUniversity.Models` ) em vez de `Data` .</span><span class="sxs-lookup"><span data-stu-id="29b50-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="29b50-799">`Models` é consistente com o código gerado pelo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="29b50-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="29b50-800">Para saber mais, confira [este problema de scaffolding do GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="29b50-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="29b50-801">O código verifica se há alunos no BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="29b50-802">Se não houver nenhum aluno no BD, o BD será inicializado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="29b50-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="29b50-803">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="29b50-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="29b50-804">O método `EnsureCreated` cria o BD automaticamente para o contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="29b50-805">Se o BD existir, `EnsureCreated` retornará sem modificar o BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="29b50-806">Em *Program.cs* , modifique o método `Main` para chamar `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="29b50-806">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="29b50-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29b50-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29b50-808">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="29b50-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="29b50-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29b50-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29b50-810">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db* .</span><span class="sxs-lookup"><span data-stu-id="29b50-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="29b50-811">Exibir o BD</span><span class="sxs-lookup"><span data-stu-id="29b50-811">View the DB</span></span>

<span data-ttu-id="29b50-812">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="29b50-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="29b50-813">Assim, o nome do banco de dados será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="29b50-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="29b50-814">O GUID será diferente para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="29b50-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="29b50-815">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29b50-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="29b50-816">No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="29b50-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span></span>

<span data-ttu-id="29b50-817">Expanda o nó **Tabelas** .</span><span class="sxs-lookup"><span data-stu-id="29b50-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="29b50-818">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="29b50-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="29b50-819">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="29b50-819">Asynchronous code</span></span>

<span data-ttu-id="29b50-820">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="29b50-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="29b50-821">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="29b50-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="29b50-822">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="29b50-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="29b50-823">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="29b50-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="29b50-824">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="29b50-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="29b50-825">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="29b50-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="29b50-826">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="29b50-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="29b50-827">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="29b50-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="29b50-828">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="29b50-829">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="29b50-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="29b50-830">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="29b50-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="29b50-831">Criar automaticamente o objeto [Task](/dotnet/api/system.threading.tasks.task) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="29b50-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="29b50-832">Para obter mais informações, consulte [Tipo de retorno de Tarefa](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="29b50-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="29b50-833">O tipo de retorno implícito `Task` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="29b50-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="29b50-834">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="29b50-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="29b50-835">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="29b50-836">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="29b50-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="29b50-837">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="29b50-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="29b50-838">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="29b50-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="29b50-839">Somente instruções que fazem com que consultas ou comandos sejam enviados ao BD são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="29b50-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="29b50-840">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="29b50-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="29b50-841">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="29b50-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="29b50-842">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="29b50-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="29b50-843">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamam métodos do EF Core que enviam consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="29b50-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="29b50-844">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="29b50-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="29b50-845">No próximo tutorial, as operações CRUD (criar, ler, atualizar e excluir) básicas são examinadas.</span><span class="sxs-lookup"><span data-stu-id="29b50-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="29b50-846">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="29b50-846">Additional resources</span></span>

* [<span data-ttu-id="29b50-847">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="29b50-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="29b50-848">Próximo</span><span class="sxs-lookup"><span data-stu-id="29b50-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
