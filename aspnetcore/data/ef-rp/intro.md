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
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365412"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="39be8-103">:::no-loc(Razor)::: Páginas com Entity Framework Core no ASP.NET Core-tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="39be8-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="39be8-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="39be8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="39be8-105">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="39be8-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="39be8-106">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="39be8-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="39be8-107">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="39be8-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="39be8-108">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="39be8-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="39be8-109">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="39be8-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="39be8-110">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="39be8-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="39be8-111">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="39be8-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39be8-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="39be8-112">Prerequisites</span></span>

* <span data-ttu-id="39be8-113">Se você for novo em :::no-loc(Razor)::: páginas, consulte a série de tutoriais [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="39be8-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="39be8-116">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-116">Database engines</span></span>

<span data-ttu-id="39be8-117">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="39be8-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="39be8-118">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="39be8-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="39be8-119">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="39be8-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="39be8-120">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="39be8-120">Troubleshooting</span></span>

<span data-ttu-id="39be8-121">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="39be8-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="39be8-122">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="39be8-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="39be8-123">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="39be8-123">The sample app</span></span>

<span data-ttu-id="39be8-124">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="39be8-125">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="39be8-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="39be8-126">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-126">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="39be8-129">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="39be8-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="39be8-130">O foco do tutorial é sobre como usar EF Core com ASP.NET Core, não como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="39be8-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="39be8-131">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="39be8-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-133">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="39be8-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="39be8-134">Selecione **ASP.NET Core aplicativo Web** a > **seguir**.</span><span class="sxs-lookup"><span data-stu-id="39be8-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="39be8-135">Nomeie o projeto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="39be8-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="39be8-136">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="39be8-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="39be8-137">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-137">Select **Create**.</span></span>
* <span data-ttu-id="39be8-138">Selecione **.NET Core** e **ASP.NET Core 5,0** nos menus suspensos e, em seguida, selecione **aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="39be8-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-140">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="39be8-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="39be8-141">Execute os seguintes comandos para criar um :::no-loc(Razor)::: projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="39be8-141">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="39be8-142">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="39be8-142">Set up the site style</span></span>

<span data-ttu-id="39be8-143">Copie e cole o código a seguir no arquivo *pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="39be8-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="39be8-144">O arquivo de layout define o cabeçalho, o rodapé e o menu do site.</span><span class="sxs-lookup"><span data-stu-id="39be8-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="39be8-145">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="39be8-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="39be8-146">Cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="39be8-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="39be8-147">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="39be8-147">There are three occurrences.</span></span>
* <span data-ttu-id="39be8-148">As entradas do menu **página inicial** e **privacidade** são excluídas.</span><span class="sxs-lookup"><span data-stu-id="39be8-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="39be8-149">As entradas são adicionadas para **about** , **estudantes** , **cursos** , **instrutores** e **departamentos**.</span><span class="sxs-lookup"><span data-stu-id="39be8-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="39be8-150">Em *pages/index. cshtml* , substitua o conteúdo do arquivo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="39be8-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="39be8-151">O código anterior substitui o texto sobre ASP.NET Core por texto sobre este aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="39be8-152">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="39be8-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="39be8-153">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-153">The data model</span></span>

<span data-ttu-id="39be8-154">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="39be8-154">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="39be8-156">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="39be8-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="39be8-157">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="39be8-157">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="39be8-159">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="39be8-160">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="39be8-161">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="39be8-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="39be8-162">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="39be8-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="39be8-163">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="39be8-164">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="39be8-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="39be8-165">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="39be8-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="39be8-166">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="39be8-167">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="39be8-168">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="39be8-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="39be8-169">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="39be8-170">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="39be8-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="39be8-171">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="39be8-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="39be8-172">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="39be8-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="39be8-173">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="39be8-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="39be8-174">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="39be8-175">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="39be8-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="39be8-176">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="39be8-176">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="39be8-178">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="39be8-179">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="39be8-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="39be8-180">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="39be8-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="39be8-181">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="39be8-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="39be8-182">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="39be8-183">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="39be8-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="39be8-184">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="39be8-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="39be8-185">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="39be8-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="39be8-186">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="39be8-187">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="39be8-188">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="39be8-189">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="39be8-190">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="39be8-191">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="39be8-192">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="39be8-193">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="39be8-194">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="39be8-194">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="39be8-196">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="39be8-197">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="39be8-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="39be8-198">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="39be8-199">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="39be8-200">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="39be8-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="39be8-201">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="39be8-201">Scaffold Student pages</span></span>

<span data-ttu-id="39be8-202">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="39be8-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="39be8-203">Uma classe de EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="39be8-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="39be8-204">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="39be8-205">Ele deriva da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="39be8-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="39be8-206">:::no-loc(Razor)::: páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-206">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-208">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="39be8-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="39be8-209">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="39be8-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="39be8-210">Na caixa de diálogo **Adicionar novo item do Scaffold** :</span><span class="sxs-lookup"><span data-stu-id="39be8-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="39be8-211">Na guia à esquerda, selecione **instalado > :::no-loc(Razor)::: páginas > comuns**</span><span class="sxs-lookup"><span data-stu-id="39be8-211">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="39be8-212">Selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-212">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="39be8-213">Na caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="39be8-213">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="39be8-214">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="39be8-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="39be8-215">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="39be8-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="39be8-216">Altere o nome do contexto de dados para terminar em `SchoolContext` em vez de `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="39be8-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="39be8-217">O nome do contexto atualizado: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="39be8-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="39be8-218">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-218">Select **Add**.</span></span>

<span data-ttu-id="39be8-219">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="39be8-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-221">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="39be8-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="39be8-222">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="39be8-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="39be8-223">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="39be8-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="39be8-224">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="39be8-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="39be8-225">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="39be8-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="39be8-226">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="39be8-227">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="39be8-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="39be8-228">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="39be8-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="39be8-229">Se a etapa anterior falhar, compile o projeto e tente a etapa Scaffold novamente.</span><span class="sxs-lookup"><span data-stu-id="39be8-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="39be8-230">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="39be8-230">The scaffolding process:</span></span>

* <span data-ttu-id="39be8-231">Cria :::no-loc(Razor)::: páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="39be8-231">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="39be8-232">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-233">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-234">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-235">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-236">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="39be8-237">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="39be8-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="39be8-238">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="39be8-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="39be8-239">Adiciona uma cadeia de conexão de banco de dados a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="39be8-239">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="39be8-240">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-240">Database connection string</span></span>

<span data-ttu-id="39be8-241">A ferramenta scaffolding gera uma cadeia de conexão no *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="39be8-241">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-243">A cadeia de conexão especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="39be8-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="39be8-244">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="39be8-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="39be8-245">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-247">Encurte a cadeia de conexão do SQLite para *cu. db* :</span><span class="sxs-lookup"><span data-stu-id="39be8-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="39be8-248">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-248">Update the database context class</span></span>

<span data-ttu-id="39be8-249">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="39be8-250">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="39be8-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="39be8-251">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="39be8-252">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="39be8-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="39be8-253">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="39be8-254">O código anterior muda do singular `DbSet<Student> Student` para o plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="39be8-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="39be8-255">Para fazer com que o :::no-loc(Razor)::: código das páginas corresponda ao novo `DBSet` nome, faça uma alteração global de: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="39be8-255">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="39be8-256">Para: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="39be8-256">to: `_context.Students.`</span></span>

<span data-ttu-id="39be8-257">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="39be8-257">There are 8 occurrences.</span></span>

<span data-ttu-id="39be8-258">Como um conjunto de entidades contém várias entidades, muitos desenvolvedores preferem que os `DBSet` nomes de propriedade devem ser plural.</span><span class="sxs-lookup"><span data-stu-id="39be8-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="39be8-259">O código realçado:</span><span class="sxs-lookup"><span data-stu-id="39be8-259">The highlighted code:</span></span>

* <span data-ttu-id="39be8-260">Cria uma [propriedade \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="39be8-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="39be8-261">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="39be8-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="39be8-262">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="39be8-263">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="39be8-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="39be8-264">Chama <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="39be8-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="39be8-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="39be8-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="39be8-266">É chamado quando foi `SchoolContext` inicializado, mas antes de o modelo ser bloqueado e usado para inicializar o contexto.</span><span class="sxs-lookup"><span data-stu-id="39be8-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="39be8-267">É necessário porque, posteriormente, no tutorial `Student` , a entidade terá referências a outras entidades.</span><span class="sxs-lookup"><span data-stu-id="39be8-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="39be8-268">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="39be8-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="39be8-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="39be8-269">Startup.cs</span></span>

<span data-ttu-id="39be8-270">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39be8-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="39be8-271">Serviços como o `SchoolContext` são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="39be8-272">Os componentes que exigem esses serviços, como :::no-loc(Razor)::: páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="39be8-272">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="39be8-273">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="39be8-274">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="39be8-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-276">As linhas realçadas a seguir foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="39be8-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-278">Verifique o código adicionado pelas chamadas scaffolder `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="39be8-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="39be8-279">Consulte [usar o SQLite para desenvolvimento, SQL Server para produção](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) para obter informações sobre como usar um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="39be8-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="39be8-280">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="39be8-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="39be8-281">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="39be8-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="39be8-282">Adicionar o filtro de exceção de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-282">Add the database exception filter</span></span>

<span data-ttu-id="39be8-283">Adicione <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> ao `ConfigureServices` conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="39be8-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="39be8-285">Adicione o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="39be8-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="39be8-286">No PMC, insira o seguinte para adicionar o pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="39be8-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="39be8-288">O `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacote NuGet fornece ASP.NET Core middleware para Entity Framework Core páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="39be8-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="39be8-289">Esse middleware ajuda a detectar e diagnosticar erros com Entity Framework Core migrações.</span><span class="sxs-lookup"><span data-stu-id="39be8-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="39be8-290">O `AddDatabaseDeveloperPageExceptionFilter` fornece informações de erro úteis no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="39be8-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="39be8-291">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-291">Create the database</span></span>

<span data-ttu-id="39be8-292">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="39be8-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="39be8-293">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="39be8-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="39be8-294">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="39be8-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="39be8-295">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="39be8-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="39be8-296">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-296">Delete the database.</span></span> <span data-ttu-id="39be8-297">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="39be8-297">Any existing data is lost.</span></span>
* <span data-ttu-id="39be8-298">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-298">Change the data model.</span></span> <span data-ttu-id="39be8-299">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="39be8-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="39be8-300">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-300">Run the app.</span></span>
* <span data-ttu-id="39be8-301">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="39be8-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="39be8-302">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="39be8-303">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="39be8-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="39be8-304">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="39be8-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="39be8-305">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="39be8-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="39be8-306">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="39be8-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="39be8-307">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39be8-307">Test the app</span></span>

* <span data-ttu-id="39be8-308">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-308">Run the app.</span></span>
* <span data-ttu-id="39be8-309">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="39be8-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="39be8-310">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="39be8-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="39be8-311">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-311">Seed the database</span></span>

<span data-ttu-id="39be8-312">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="39be8-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="39be8-313">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="39be8-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="39be8-314">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="39be8-315">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="39be8-316">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="39be8-317">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="39be8-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="39be8-318">Em *Program.cs* , substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="39be8-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-320">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="39be8-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="39be8-321">Responda com `Y` para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-323">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="39be8-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="39be8-324">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-324">Restart the app.</span></span>
* <span data-ttu-id="39be8-325">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="39be8-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="39be8-326">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-328">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="39be8-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="39be8-329">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="39be8-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="39be8-330">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="39be8-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="39be8-331">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="39be8-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="39be8-332">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="39be8-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="39be8-333">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-335">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="39be8-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="39be8-336">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="39be8-337">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="39be8-337">Asynchronous code</span></span>

<span data-ttu-id="39be8-338">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="39be8-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="39be8-339">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="39be8-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="39be8-340">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="39be8-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="39be8-341">Com o código síncrono, muitos threads podem ser ligados enquanto não estão trabalhando porque estão aguardando a conclusão de e/s.</span><span class="sxs-lookup"><span data-stu-id="39be8-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="39be8-342">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="39be8-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="39be8-343">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="39be8-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="39be8-344">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="39be8-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="39be8-345">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="39be8-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="39be8-346">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="39be8-347">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="39be8-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="39be8-348">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="39be8-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="39be8-349">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="39be8-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="39be8-350">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="39be8-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="39be8-351">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="39be8-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="39be8-352">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="39be8-353">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="39be8-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="39be8-354">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="39be8-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="39be8-355">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="39be8-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="39be8-356">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="39be8-357">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="39be8-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="39be8-358">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="39be8-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="39be8-359">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="39be8-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="39be8-360">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="39be8-361">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="39be8-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="39be8-362">Considerações sobre o desempenho</span><span class="sxs-lookup"><span data-stu-id="39be8-362">Performance considerations</span></span>

<span data-ttu-id="39be8-363">Em geral, uma página da Web não deve carregar um número arbitrário de linhas.</span><span class="sxs-lookup"><span data-stu-id="39be8-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="39be8-364">Uma consulta deve usar a paginação ou uma abordagem de limitação.</span><span class="sxs-lookup"><span data-stu-id="39be8-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="39be8-365">Por exemplo, a consulta anterior poderia usar `Take` para limitar as linhas retornadas:</span><span class="sxs-lookup"><span data-stu-id="39be8-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="39be8-366">Enumerar uma tabela grande em uma exibição pode retornar uma resposta HTTP 200 parcialmente construída se uma exceção de banco de dados ocorrer parcialmente por meio da enumeração.</span><span class="sxs-lookup"><span data-stu-id="39be8-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="39be8-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> o padrão é 1024.</span><span class="sxs-lookup"><span data-stu-id="39be8-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="39be8-368">Os seguintes conjuntos de código `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="39be8-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="39be8-369">A paginação será abordada posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="39be8-370">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="39be8-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="39be8-371">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="39be8-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="39be8-372">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="39be8-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="39be8-373">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="39be8-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="39be8-374">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="39be8-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="39be8-375">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="39be8-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="39be8-376">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="39be8-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="39be8-377">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="39be8-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="39be8-378">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="39be8-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39be8-379">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="39be8-379">Prerequisites</span></span>

* <span data-ttu-id="39be8-380">Se você for novo em :::no-loc(Razor)::: páginas, consulte a série de tutoriais [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="39be8-380">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="39be8-383">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-383">Database engines</span></span>

<span data-ttu-id="39be8-384">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="39be8-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="39be8-385">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="39be8-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="39be8-386">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="39be8-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="39be8-387">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="39be8-387">Troubleshooting</span></span>

<span data-ttu-id="39be8-388">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="39be8-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="39be8-389">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="39be8-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="39be8-390">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="39be8-390">The sample app</span></span>

<span data-ttu-id="39be8-391">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="39be8-392">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="39be8-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="39be8-393">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-393">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="39be8-396">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="39be8-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="39be8-397">O foco do tutorial está em como usar o EF Core, não em como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="39be8-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="39be8-398">Siga o link na parte superior da página para obter o código-fonte do projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="39be8-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="39be8-399">A pasta *cu30* tem o código para a versão ASP.NET Core 3.0 do tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="39be8-400">Os arquivos que refletem o estado do código para os tutoriais 1-7 podem ser encontrados na pasta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="39be8-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-402">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="39be8-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="39be8-403">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-403">Build the project.</span></span>
* <span data-ttu-id="39be8-404">No PMC (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="39be8-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="39be8-405">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-407">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="39be8-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="39be8-408">Exclua *ContosoUniversity.csproj* e altere o nome de *ContosoUniversitySQLite.csproj* para *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="39be8-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="39be8-409">No *Program.cs* , comente `#define Startup` que `StartupSQLite` é usado.</span><span class="sxs-lookup"><span data-stu-id="39be8-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="39be8-410">Exclua *appSettings.json* e altere o nome de *appSettingsSQLite.json* para *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="39be8-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="39be8-411">Exclua a pasta *Migrations* e altere o nome de *MigrationsSQL* para *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="39be8-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="39be8-412">Faça uma pesquisa global para `#if SQLiteVersion` e remova `#if SQLiteVersion` e a `#endif` instrução associada.</span><span class="sxs-lookup"><span data-stu-id="39be8-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="39be8-413">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-413">Build the project.</span></span>
* <span data-ttu-id="39be8-414">Em um prompt de comando na pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="39be8-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="39be8-415">Em sua ferramenta do SQLite, execute a seguinte instrução SQL:</span><span class="sxs-lookup"><span data-stu-id="39be8-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="39be8-416">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="39be8-417">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="39be8-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-419">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="39be8-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="39be8-420">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="39be8-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="39be8-421">Nomeie o projeto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="39be8-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="39be8-422">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="39be8-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="39be8-423">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="39be8-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-425">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="39be8-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="39be8-426">Execute os seguintes comandos para criar um :::no-loc(Razor)::: projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="39be8-426">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="39be8-427">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="39be8-427">Set up the site style</span></span>

<span data-ttu-id="39be8-428">Configure o cabeçalho, o rodapé e o menu do site atualizando *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="39be8-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="39be8-429">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="39be8-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="39be8-430">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="39be8-430">There are three occurrences.</span></span>

* <span data-ttu-id="39be8-431">Exclua as entradas de menu **Início** e **Privacidade** , então adicione as entradas para **Sobre** , **Alunos** , **Cursos** , **Instrutores** e **Departamentos**.</span><span class="sxs-lookup"><span data-stu-id="39be8-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="39be8-432">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="39be8-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="39be8-433">Em *Pages/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET Core pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="39be8-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="39be8-434">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="39be8-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="39be8-435">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-435">The data model</span></span>

<span data-ttu-id="39be8-436">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="39be8-436">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="39be8-438">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="39be8-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="39be8-439">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="39be8-439">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="39be8-441">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="39be8-442">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="39be8-443">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="39be8-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="39be8-444">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="39be8-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="39be8-445">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="39be8-446">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="39be8-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="39be8-447">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="39be8-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="39be8-448">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="39be8-449">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="39be8-450">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="39be8-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="39be8-451">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="39be8-452">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="39be8-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="39be8-453">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="39be8-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="39be8-454">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="39be8-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="39be8-455">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="39be8-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="39be8-456">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="39be8-457">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="39be8-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="39be8-458">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="39be8-458">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="39be8-460">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="39be8-461">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="39be8-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="39be8-462">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="39be8-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="39be8-463">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="39be8-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="39be8-464">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="39be8-465">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="39be8-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="39be8-466">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="39be8-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="39be8-467">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="39be8-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="39be8-468">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="39be8-469">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="39be8-470">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="39be8-471">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="39be8-472">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="39be8-473">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="39be8-474">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="39be8-475">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="39be8-476">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="39be8-476">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="39be8-478">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="39be8-479">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="39be8-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="39be8-480">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="39be8-481">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="39be8-482">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="39be8-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="39be8-483">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="39be8-483">Scaffold Student pages</span></span>

<span data-ttu-id="39be8-484">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="39be8-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="39be8-485">Uma classe *contexto* do EF Core.</span><span class="sxs-lookup"><span data-stu-id="39be8-485">An EF Core *context* class.</span></span> <span data-ttu-id="39be8-486">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="39be8-487">Ele deriva da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="39be8-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="39be8-488">:::no-loc(Razor)::: páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-488">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-490">Crie uma pasta *Alunos* na pasta *Páginas*.</span><span class="sxs-lookup"><span data-stu-id="39be8-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="39be8-491">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="39be8-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="39be8-492">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-492">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="39be8-493">Na caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="39be8-493">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="39be8-494">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="39be8-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="39be8-495">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="39be8-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="39be8-496">Altere o nome do contexto de dados de *ContosoUniversity.Models.ContosoUniversityContext* para *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="39be8-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="39be8-497">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-497">Select **Add**.</span></span>

<span data-ttu-id="39be8-498">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="39be8-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-500">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="39be8-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="39be8-501">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="39be8-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="39be8-502">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="39be8-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="39be8-503">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="39be8-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="39be8-504">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="39be8-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="39be8-505">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="39be8-506">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="39be8-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="39be8-507">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="39be8-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="39be8-508">Se você tiver um problema com a etapa anterior, compile o projeto e repita a etapa de scaffold.</span><span class="sxs-lookup"><span data-stu-id="39be8-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="39be8-509">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="39be8-509">The scaffolding process:</span></span>

* <span data-ttu-id="39be8-510">Cria :::no-loc(Razor)::: páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="39be8-510">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="39be8-511">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-512">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-513">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-514">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="39be8-515">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="39be8-516">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="39be8-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="39be8-517">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="39be8-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="39be8-518">Adiciona uma cadeia de conexão de banco de dados a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="39be8-518">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="39be8-519">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-521">O *:::no-loc(appsettings.json):::* arquivo especifica a cadeia de conexão [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="39be8-521">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="39be8-522">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="39be8-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="39be8-523">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-525">Altere a cadeia de conexão para apontar para um arquivo de banco de dados SQLite chamado *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="39be8-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="39be8-526">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-526">Update the database context class</span></span>

<span data-ttu-id="39be8-527">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="39be8-528">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="39be8-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="39be8-529">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="39be8-530">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="39be8-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="39be8-531">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="39be8-532">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="39be8-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="39be8-533">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="39be8-533">In EF Core terminology:</span></span>

* <span data-ttu-id="39be8-534">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="39be8-535">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="39be8-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="39be8-536">Como um conjunto de entidades contém várias entidades, as propriedades DBSet devem ser nomes no plural.</span><span class="sxs-lookup"><span data-stu-id="39be8-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="39be8-537">Como a ferramenta scaffolding criou um`Student` DBSet, essa etapa o altera para o `Students` no plural.</span><span class="sxs-lookup"><span data-stu-id="39be8-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="39be8-538">Para fazer com que o :::no-loc(Razor)::: código das páginas corresponda ao novo nome de DBSet, faça uma alteração global em todo o projeto de `_context.Student` para `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="39be8-538">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="39be8-539">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="39be8-539">There are 8 occurrences.</span></span>

<span data-ttu-id="39be8-540">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="39be8-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="39be8-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="39be8-541">Startup.cs</span></span>

<span data-ttu-id="39be8-542">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39be8-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="39be8-543">Serviços (como o contexto de banco de dados do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="39be8-544">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="39be8-544">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="39be8-545">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="39be8-546">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="39be8-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-548">Em `ConfigureServices`, as linhas destacadas foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="39be8-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-550">Em `ConfigureServices`, verifique se o código adicionado pelo scaffolder chama `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="39be8-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="39be8-551">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="39be8-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="39be8-552">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="39be8-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="39be8-553">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-553">Create the database</span></span>

<span data-ttu-id="39be8-554">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="39be8-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="39be8-555">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="39be8-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="39be8-556">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="39be8-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="39be8-557">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="39be8-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="39be8-558">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-558">Delete the database.</span></span> <span data-ttu-id="39be8-559">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="39be8-559">Any existing data is lost.</span></span>
* <span data-ttu-id="39be8-560">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-560">Change the data model.</span></span> <span data-ttu-id="39be8-561">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="39be8-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="39be8-562">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-562">Run the app.</span></span>
* <span data-ttu-id="39be8-563">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="39be8-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="39be8-564">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="39be8-565">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="39be8-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="39be8-566">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="39be8-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="39be8-567">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="39be8-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="39be8-568">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="39be8-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="39be8-569">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39be8-569">Test the app</span></span>

* <span data-ttu-id="39be8-570">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-570">Run the app.</span></span>
* <span data-ttu-id="39be8-571">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="39be8-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="39be8-572">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="39be8-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="39be8-573">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-573">Seed the database</span></span>

<span data-ttu-id="39be8-574">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="39be8-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="39be8-575">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="39be8-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="39be8-576">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="39be8-577">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="39be8-578">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="39be8-579">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="39be8-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="39be8-580">Em *Program.cs* , substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="39be8-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-582">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="39be8-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-584">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="39be8-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="39be8-585">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-585">Restart the app.</span></span>

* <span data-ttu-id="39be8-586">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="39be8-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="39be8-587">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-589">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="39be8-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="39be8-590">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="39be8-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="39be8-591">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="39be8-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="39be8-592">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="39be8-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="39be8-593">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="39be8-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="39be8-594">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-596">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="39be8-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="39be8-597">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="39be8-598">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="39be8-598">Asynchronous code</span></span>

<span data-ttu-id="39be8-599">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="39be8-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="39be8-600">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="39be8-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="39be8-601">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="39be8-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="39be8-602">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="39be8-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="39be8-603">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="39be8-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="39be8-604">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="39be8-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="39be8-605">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="39be8-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="39be8-606">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="39be8-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="39be8-607">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="39be8-608">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="39be8-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="39be8-609">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="39be8-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="39be8-610">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="39be8-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="39be8-611">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="39be8-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="39be8-612">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="39be8-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="39be8-613">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="39be8-614">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="39be8-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="39be8-615">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="39be8-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="39be8-616">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="39be8-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="39be8-617">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="39be8-618">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="39be8-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="39be8-619">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="39be8-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="39be8-620">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="39be8-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="39be8-621">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="39be8-622">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="39be8-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="39be8-623">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="39be8-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="39be8-624">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="39be8-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39be8-625">O aplicativo Web de exemplo Contoso University demonstra como criar um :::no-loc(Razor)::: aplicativo de páginas ASP.NET Core usando Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="39be8-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="39be8-626">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="39be8-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="39be8-627">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="39be8-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="39be8-628">Esta página é a primeira de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="39be8-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="39be8-629">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="39be8-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="39be8-630">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="39be8-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39be8-631">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="39be8-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="39be8-634">Familiaridade com [ :::no-loc(Razor)::: páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="39be8-634">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="39be8-635">Os novos programadores devem concluir a [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta série.</span><span class="sxs-lookup"><span data-stu-id="39be8-635">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="39be8-636">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="39be8-636">Troubleshooting</span></span>

<span data-ttu-id="39be8-637">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="39be8-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="39be8-638">Uma boa maneira de obter ajuda é postando uma pergunta no [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="39be8-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="39be8-639">O aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="39be8-639">The Contoso University web app</span></span>

<span data-ttu-id="39be8-640">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="39be8-641">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="39be8-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="39be8-642">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-642">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

<span data-ttu-id="39be8-645">O estilo de interface do usuário deste site é próximo ao que é gerado pelos modelos internos.</span><span class="sxs-lookup"><span data-stu-id="39be8-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="39be8-646">O foco do tutorial está em EF Core com :::no-loc(Razor)::: páginas, não na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="39be8-646">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="39be8-647">Criar o :::no-loc(Razor)::: aplicativo Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="39be8-647">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-649">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="39be8-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="39be8-650">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="39be8-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="39be8-651">Nomeie o projeto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="39be8-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="39be8-652">É importante nomear o projeto *ContosoUniversity* para que os namespaces sejam correspondentes quando o código for copiado/colado.</span><span class="sxs-lookup"><span data-stu-id="39be8-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="39be8-653">Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="39be8-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="39be8-654">Para imagens das etapas anteriores, consulte [criar um :::no-loc(Razor)::: aplicativo Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="39be8-654">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="39be8-655">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="39be8-657">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="39be8-657">Set up the site style</span></span>

<span data-ttu-id="39be8-658">Algumas alterações configuram o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="39be8-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="39be8-659">Atualize *Pages/Shared/_Layout.cshtml* com as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="39be8-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="39be8-660">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="39be8-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="39be8-661">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="39be8-661">There are three occurrences.</span></span>

* <span data-ttu-id="39be8-662">Adicione entradas de menu para **Alunos** , **Cursos** , **Instrutores** e **Departamentos** e exclua a entrada de menu **Contato**.</span><span class="sxs-lookup"><span data-stu-id="39be8-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="39be8-663">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="39be8-663">The changes are highlighted.</span></span> <span data-ttu-id="39be8-664">(Toda a marcação *não* é exibida.)</span><span class="sxs-lookup"><span data-stu-id="39be8-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="39be8-665">Em *Pages/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="39be8-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="39be8-666">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="39be8-666">Create the data model</span></span>

<span data-ttu-id="39be8-667">Crie classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="39be8-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="39be8-668">Comece com as três seguintes entidades:</span><span class="sxs-lookup"><span data-stu-id="39be8-668">Start with the following three entities:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="39be8-670">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="39be8-671">Há uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="39be8-672">Um aluno pode se registrar em qualquer quantidade de cursos.</span><span class="sxs-lookup"><span data-stu-id="39be8-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="39be8-673">Um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="39be8-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="39be8-674">Nas seções a seguir, é criada uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="39be8-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="39be8-675">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="39be8-675">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="39be8-677">Crie uma pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="39be8-677">Create a *Models* folder.</span></span> <span data-ttu-id="39be8-678">Na pasta *Models* , crie um arquivo de classe chamado *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="39be8-679">A propriedade `ID` se torna a coluna de chave primária da tabela de BD (banco de dados) que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="39be8-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="39be8-680">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="39be8-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="39be8-681">Em `classnameID`, `classname` é o nome da classe.</span><span class="sxs-lookup"><span data-stu-id="39be8-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="39be8-682">A chave primária alternativa reconhecida automaticamente é `StudentID` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="39be8-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="39be8-683">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="39be8-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="39be8-684">As propriedades de navegação vinculam-se a outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="39be8-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="39be8-685">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazena todas as entidades `Enrollment` relacionadas a essa `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="39be8-686">Por exemplo, se uma linha Aluno no BD tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="39be8-687">Uma linha `Enrollment` relacionada é uma linha que contém o valor de chave primária do aluno na coluna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="39be8-688">Por exemplo, suponha que o aluno com ID=1 tenha duas linhas na tabela `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="39be8-689">A tabela `Enrollment` tem duas linhas com `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="39be8-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="39be8-690">`StudentID` é uma chave estrangeira na tabela `Enrollment` que especifica o aluno na tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="39be8-691">Se uma propriedade de navegação puder armazenar várias entidades, a propriedade de navegação deverá ser um tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="39be8-692">`ICollection<T>` pode ser especificado ou um tipo como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="39be8-693">Quando `ICollection<T>` é usado, o EF Core cria uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="39be8-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="39be8-694">As propriedades de navegação que armazenam várias entidades são provenientes de relações muitos para muitos e um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="39be8-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="39be8-695">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="39be8-695">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="39be8-697">Na pasta *Models* , crie *Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="39be8-698">A propriedade `EnrollmentID` é a chave primária.</span><span class="sxs-lookup"><span data-stu-id="39be8-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="39be8-699">Essa entidade usa o padrão `classnameID` em vez de `ID` como a entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="39be8-700">Normalmente, os desenvolvedores escolhem um padrão e o usam em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="39be8-701">Em um tutorial posterior, o uso de uma ID sem nome de classe é mostrado para facilitar a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="39be8-702">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="39be8-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="39be8-703">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="39be8-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="39be8-704">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="39be8-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="39be8-705">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="39be8-706">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="39be8-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="39be8-707">A entidade `Student` é distinta da propriedade de navegação `Student.Enrollments`, que contém várias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="39be8-708">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="39be8-709">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="39be8-710">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="39be8-711">Por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="39be8-712">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="39be8-713">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="39be8-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="39be8-714">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="39be8-714">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="39be8-716">Na pasta *Models* , crie *Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="39be8-717">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="39be8-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="39be8-718">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="39be8-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="39be8-719">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="39be8-720">Gere um modelo de aluno por scaffold</span><span class="sxs-lookup"><span data-stu-id="39be8-720">Scaffold the student model</span></span>

<span data-ttu-id="39be8-721">Nesta seção, é feito o scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="39be8-722">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="39be8-723">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="39be8-723">Build the project.</span></span>
* <span data-ttu-id="39be8-724">Crie a pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="39be8-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39be8-726">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Pages/Students* > **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="39be8-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="39be8-727">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-727">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="39be8-728">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="39be8-728">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="39be8-729">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="39be8-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="39be8-730">Na linha **Classe de contexto de dados** , selecione o sinal de (mais) **+** e altere o nome gerado para **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="39be8-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="39be8-731">Na lista suspensa **Classe de contexto de dados** , selecione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="39be8-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="39be8-732">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="39be8-732">Select **Add**.</span></span>

![Caixa de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="39be8-734">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se tiver problemas na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="39be8-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39be8-736">Execute os comandos a seguir para fazer scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="39be8-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="39be8-737">O processo de scaffold criou e alterou os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="39be8-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="39be8-738">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="39be8-738">Files created</span></span>

* <span data-ttu-id="39be8-739">*Pages/Students* Criar, Excluir, Detalhes, Editar, Índice.</span><span class="sxs-lookup"><span data-stu-id="39be8-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="39be8-740">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="39be8-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="39be8-741">Atualizações de arquivo</span><span class="sxs-lookup"><span data-stu-id="39be8-741">File updates</span></span>

* <span data-ttu-id="39be8-742">*Startup.cs* : alterações a esse arquivo serão detalhadas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="39be8-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="39be8-743">*:::no-loc(appsettings.json):::* : A cadeia de conexão usada para se conectar a um banco de dados local é adicionada.</span><span class="sxs-lookup"><span data-stu-id="39be8-743">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="39be8-744">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="39be8-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="39be8-745">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39be8-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="39be8-746">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="39be8-747">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="39be8-747">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="39be8-748">O código de construtor que obtém uma instância de contexto do BD é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39be8-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="39be8-749">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="39be8-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="39be8-750">Examine o método `ConfigureServices` em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="39be8-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="39be8-751">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="39be8-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="39be8-752">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="39be8-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="39be8-753">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="39be8-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="39be8-754">Atualizar o principal</span><span class="sxs-lookup"><span data-stu-id="39be8-754">Update main</span></span>

<span data-ttu-id="39be8-755">Em *Program.cs* , modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="39be8-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="39be8-756">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="39be8-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="39be8-757">Chame o [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="39be8-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="39be8-758">Descarte o contexto quando o método `EnsureCreated` for concluído.</span><span class="sxs-lookup"><span data-stu-id="39be8-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="39be8-759">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="39be8-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="39be8-760">`EnsureCreated` garante que o banco de dados do contexto exista.</span><span class="sxs-lookup"><span data-stu-id="39be8-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="39be8-761">Se ele existir, nenhuma ação será realizada.</span><span class="sxs-lookup"><span data-stu-id="39be8-761">If it exists, no action is taken.</span></span> <span data-ttu-id="39be8-762">Se ele não existir, o banco de dados e todos os seus esquemas serão criados.</span><span class="sxs-lookup"><span data-stu-id="39be8-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="39be8-763">`EnsureCreated` não usa migrações para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="39be8-764">Um banco de dados criado com `EnsureCreated` não pode ser atualizado posteriormente usando migrações.</span><span class="sxs-lookup"><span data-stu-id="39be8-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="39be8-765">`EnsureCreated` é chamado na inicialização do aplicativo, que permite que o seguinte fluxo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="39be8-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="39be8-766">Exclua o BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-766">Delete the DB.</span></span>
* <span data-ttu-id="39be8-767">Altere o esquema de BD (por exemplo, adicione um campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="39be8-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="39be8-768">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39be8-768">Run the app.</span></span>
* <span data-ttu-id="39be8-769">`EnsureCreated` cria um BD com a coluna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="39be8-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="39be8-770">`EnsureCreated` é conveniente no início do desenvolvimento quando o esquema está evoluindo rapidamente.</span><span class="sxs-lookup"><span data-stu-id="39be8-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="39be8-771">Mais tarde, no tutorial do banco de dados, é excluído e as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="39be8-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="39be8-772">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39be8-772">Test the app</span></span>

<span data-ttu-id="39be8-773">Execute o aplicativo e aceite a :::no-loc(cookie)::: política.</span><span class="sxs-lookup"><span data-stu-id="39be8-773">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="39be8-774">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="39be8-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="39be8-775">Você pode ler sobre a :::no-loc(cookie)::: política no [suporte de regulamento geral sobre a proteção de dados da UE (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="39be8-775">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="39be8-776">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="39be8-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="39be8-777">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="39be8-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="39be8-778">Examine o contexto de BD SchoolContext</span><span class="sxs-lookup"><span data-stu-id="39be8-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="39be8-779">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="39be8-780">O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="39be8-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="39be8-781">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="39be8-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="39be8-782">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="39be8-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="39be8-783">Atualize *SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="39be8-784">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="39be8-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="39be8-785">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="39be8-785">In EF Core terminology:</span></span>

* <span data-ttu-id="39be8-786">Um conjunto de entidades normalmente corresponde a uma tabela de BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="39be8-787">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="39be8-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="39be8-788">`DbSet<Enrollment>` e `DbSet<Course>` podem ser omitidos.</span><span class="sxs-lookup"><span data-stu-id="39be8-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="39be8-789">O EF Core inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="39be8-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="39be8-790">Para este tutorial, mantenha `DbSet<Enrollment>` e `DbSet<Course>` no `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="39be8-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="39be8-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="39be8-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="39be8-792">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="39be8-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="39be8-793">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="39be8-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="39be8-794">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="39be8-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="39be8-795">Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="39be8-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="39be8-796">Adicionar um código para inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="39be8-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="39be8-797">O EF Core cria um BD vazio.</span><span class="sxs-lookup"><span data-stu-id="39be8-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="39be8-798">Nesta seção, um método `Initialize` é escrito para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="39be8-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="39be8-799">Na pasta *Dados* , crie um novo arquivo de classe chamado *DbInitializer.cs* e adicione o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="39be8-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="39be8-800">Observação: o código anterior usa `Models` para o namespace ( `namespace ContosoUniversity.Models` ) em vez de `Data` .</span><span class="sxs-lookup"><span data-stu-id="39be8-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="39be8-801">`Models` é consistente com o código gerado pelo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="39be8-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="39be8-802">Para saber mais, confira [este problema de scaffolding do GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="39be8-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="39be8-803">O código verifica se há alunos no BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="39be8-804">Se não houver nenhum aluno no BD, o BD será inicializado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="39be8-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="39be8-805">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="39be8-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="39be8-806">O método `EnsureCreated` cria o BD automaticamente para o contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="39be8-807">Se o BD existir, `EnsureCreated` retornará sem modificar o BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="39be8-808">Em *Program.cs* , modifique o método `Main` para chamar `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="39be8-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="39be8-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39be8-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39be8-810">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="39be8-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="39be8-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39be8-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39be8-812">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="39be8-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="39be8-813">Exibir o BD</span><span class="sxs-lookup"><span data-stu-id="39be8-813">View the DB</span></span>

<span data-ttu-id="39be8-814">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="39be8-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="39be8-815">Assim, o nome do banco de dados será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="39be8-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="39be8-816">O GUID será diferente para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="39be8-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="39be8-817">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="39be8-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="39be8-818">No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="39be8-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="39be8-819">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="39be8-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="39be8-820">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="39be8-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="39be8-821">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="39be8-821">Asynchronous code</span></span>

<span data-ttu-id="39be8-822">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="39be8-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="39be8-823">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="39be8-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="39be8-824">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="39be8-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="39be8-825">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="39be8-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="39be8-826">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="39be8-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="39be8-827">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="39be8-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="39be8-828">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="39be8-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="39be8-829">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="39be8-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="39be8-830">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="39be8-831">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="39be8-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="39be8-832">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="39be8-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="39be8-833">Criar automaticamente o objeto [Task](/dotnet/api/system.threading.tasks.task) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="39be8-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="39be8-834">Para obter mais informações, consulte [Tipo de retorno de Tarefa](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="39be8-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="39be8-835">O tipo de retorno implícito `Task` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="39be8-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="39be8-836">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="39be8-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="39be8-837">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="39be8-838">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="39be8-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="39be8-839">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="39be8-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="39be8-840">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="39be8-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="39be8-841">Somente instruções que fazem com que consultas ou comandos sejam enviados ao BD são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39be8-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="39be8-842">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="39be8-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="39be8-843">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="39be8-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="39be8-844">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="39be8-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="39be8-845">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamam métodos do EF Core que enviam consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="39be8-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="39be8-846">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="39be8-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="39be8-847">No próximo tutorial, as operações CRUD (criar, ler, atualizar e excluir) básicas são examinadas.</span><span class="sxs-lookup"><span data-stu-id="39be8-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="39be8-848">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="39be8-848">Additional resources</span></span>

* [<span data-ttu-id="39be8-849">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="39be8-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="39be8-850">Próximo</span><span class="sxs-lookup"><span data-stu-id="39be8-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
