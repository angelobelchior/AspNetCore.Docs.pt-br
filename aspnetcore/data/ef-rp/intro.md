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
ms.openlocfilehash: 5849f4bfb9d0355177ceb5c2f4236c6d39dd9e92
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340030"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="ac288-103">:::no-loc(Razor)::: Páginas com Entity Framework Core no ASP.NET Core-tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="ac288-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="ac288-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ac288-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ac288-105">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="ac288-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="ac288-106">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="ac288-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ac288-107">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="ac288-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ac288-108">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="ac288-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="ac288-109">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="ac288-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="ac288-110">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="ac288-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ac288-111">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ac288-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ac288-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ac288-112">Prerequisites</span></span>

* <span data-ttu-id="ac288-113">Se você for novo em :::no-loc(Razor)::: páginas, consulte a série de tutoriais [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="ac288-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="ac288-116">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-116">Database engines</span></span>

<span data-ttu-id="ac288-117">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="ac288-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="ac288-118">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ac288-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="ac288-119">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="ac288-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ac288-120">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="ac288-120">Troubleshooting</span></span>

<span data-ttu-id="ac288-121">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ac288-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ac288-122">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="ac288-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="ac288-123">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="ac288-123">The sample app</span></span>

<span data-ttu-id="ac288-124">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="ac288-125">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="ac288-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ac288-126">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-126">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="ac288-129">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="ac288-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="ac288-130">O foco do tutorial é sobre como usar EF Core com ASP.NET Core, não como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="ac288-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="ac288-131">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ac288-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ac288-133">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="ac288-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="ac288-134">Na caixa de diálogo **criar um novo projeto** , selecione **ASP.NET Core aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="ac288-135">Na caixa de diálogo **configurar seu novo projeto** , digite `ContosoUniversity` para **nome do projeto**.</span><span class="sxs-lookup"><span data-stu-id="ac288-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="ac288-136">É importante usar esse nome exato, incluindo a capitalização, para que cada `namespace` correspondência seja feita quando o código for copiado.</span><span class="sxs-lookup"><span data-stu-id="ac288-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="ac288-137">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-137">Select **Create**.</span></span>
1. <span data-ttu-id="ac288-138">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:</span><span class="sxs-lookup"><span data-stu-id="ac288-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="ac288-139">**.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="ac288-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="ac288-140">**ASP.NET Core aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="ac288-140">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="ac288-141">**Create** 
       Criar ![ Caixa de diálogo novo projeto de ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="ac288-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-143">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="ac288-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="ac288-144">Execute os seguintes comandos para criar um :::no-loc(Razor)::: projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="ac288-144">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ac288-145">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="ac288-145">Set up the site style</span></span>

<span data-ttu-id="ac288-146">Copie e cole o código a seguir no arquivo *pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="ac288-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="ac288-147">O arquivo de layout define o cabeçalho, o rodapé e o menu do site.</span><span class="sxs-lookup"><span data-stu-id="ac288-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="ac288-148">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="ac288-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac288-149">Cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="ac288-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ac288-150">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="ac288-150">There are three occurrences.</span></span>
* <span data-ttu-id="ac288-151">As entradas do menu **página inicial** e **privacidade** são excluídas.</span><span class="sxs-lookup"><span data-stu-id="ac288-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="ac288-152">As entradas são adicionadas para **about** , **estudantes** , **cursos** , **instrutores** e **departamentos**.</span><span class="sxs-lookup"><span data-stu-id="ac288-152">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="ac288-153">Em *pages/index. cshtml* , substitua o conteúdo do arquivo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac288-153">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="ac288-154">O código anterior substitui o texto sobre ASP.NET Core por texto sobre este aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="ac288-155">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="ac288-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="ac288-156">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-156">The data model</span></span>

<span data-ttu-id="ac288-157">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="ac288-157">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="ac288-159">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="ac288-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="ac288-160">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="ac288-160">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="ac288-162">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="ac288-163">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="ac288-164">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="ac288-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="ac288-165">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="ac288-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ac288-166">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="ac288-167">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="ac288-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="ac288-168">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ac288-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ac288-169">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="ac288-170">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="ac288-171">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="ac288-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="ac288-172">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="ac288-173">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="ac288-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="ac288-174">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="ac288-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="ac288-175">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="ac288-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="ac288-176">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="ac288-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="ac288-177">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="ac288-178">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac288-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="ac288-179">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="ac288-179">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="ac288-181">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="ac288-182">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="ac288-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="ac288-183">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="ac288-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="ac288-184">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="ac288-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="ac288-185">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="ac288-186">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="ac288-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ac288-187">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="ac288-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="ac288-188">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="ac288-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ac288-189">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ac288-190">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="ac288-191">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ac288-192">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ac288-193">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ac288-194">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ac288-195">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ac288-196">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ac288-197">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="ac288-197">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="ac288-199">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="ac288-200">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="ac288-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ac288-201">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ac288-202">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="ac288-203">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="ac288-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="ac288-204">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="ac288-204">Scaffold Student pages</span></span>

<span data-ttu-id="ac288-205">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="ac288-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="ac288-206">Uma classe de EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="ac288-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="ac288-207">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="ac288-208">Ele deriva da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ac288-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="ac288-209">:::no-loc(Razor)::: páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-209">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-211">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ac288-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="ac288-212">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="ac288-212">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ac288-213">Na caixa de diálogo **Adicionar novo item do Scaffold** :</span><span class="sxs-lookup"><span data-stu-id="ac288-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="ac288-214">Na guia à esquerda, selecione **instalado > :::no-loc(Razor)::: páginas > comuns**</span><span class="sxs-lookup"><span data-stu-id="ac288-214">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="ac288-215">Selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-215">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="ac288-216">Na caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="ac288-216">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="ac288-217">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="ac288-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="ac288-218">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="ac288-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="ac288-219">Altere o nome do contexto de dados para terminar em `SchoolContext` em vez de `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="ac288-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="ac288-220">O nome do contexto atualizado: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="ac288-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="ac288-221">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-221">Select **Add**.</span></span>

<span data-ttu-id="ac288-222">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="ac288-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-224">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="ac288-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="ac288-225">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="ac288-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="ac288-226">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ac288-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="ac288-227">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ac288-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="ac288-228">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="ac288-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="ac288-229">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="ac288-230">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="ac288-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="ac288-231">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="ac288-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="ac288-232">Se a etapa anterior falhar, compile o projeto e tente a etapa Scaffold novamente.</span><span class="sxs-lookup"><span data-stu-id="ac288-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="ac288-233">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="ac288-233">The scaffolding process:</span></span>

* <span data-ttu-id="ac288-234">Cria :::no-loc(Razor)::: páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="ac288-234">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="ac288-235">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-236">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-237">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-238">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-239">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="ac288-240">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac288-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="ac288-241">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac288-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="ac288-242">Adiciona uma cadeia de conexão de banco de dados a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="ac288-242">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="ac288-243">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-243">Database connection string</span></span>

<span data-ttu-id="ac288-244">A ferramenta scaffolding gera uma cadeia de conexão no *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="ac288-244">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-246">A cadeia de conexão especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="ac288-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="ac288-247">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="ac288-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ac288-248">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-250">Encurte a cadeia de conexão do SQLite para *cu. db* :</span><span class="sxs-lookup"><span data-stu-id="ac288-250">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="ac288-251">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-251">Update the database context class</span></span>

<span data-ttu-id="ac288-252">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="ac288-253">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="ac288-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ac288-254">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ac288-255">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ac288-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ac288-256">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="ac288-257">O código anterior muda do singular `DbSet<Student> Student` para o plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="ac288-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="ac288-258">Para fazer com que o :::no-loc(Razor)::: código das páginas corresponda ao novo `DBSet` nome, faça uma alteração global de: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="ac288-258">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="ac288-259">Para: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="ac288-259">to: `_context.Students.`</span></span>

<span data-ttu-id="ac288-260">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="ac288-260">There are 8 occurrences.</span></span>

<span data-ttu-id="ac288-261">Como um conjunto de entidades contém várias entidades, muitos desenvolvedores preferem que os `DBSet` nomes de propriedade devem ser plural.</span><span class="sxs-lookup"><span data-stu-id="ac288-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="ac288-262">O código realçado:</span><span class="sxs-lookup"><span data-stu-id="ac288-262">The highlighted code:</span></span>

* <span data-ttu-id="ac288-263">Cria uma [propriedade \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="ac288-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ac288-264">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="ac288-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="ac288-265">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="ac288-266">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="ac288-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="ac288-267">Chama <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac288-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="ac288-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="ac288-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="ac288-269">É chamado quando foi `SchoolContext` inicializado, mas antes de o modelo ser bloqueado e usado para inicializar o contexto.</span><span class="sxs-lookup"><span data-stu-id="ac288-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="ac288-270">É necessário porque, posteriormente, no tutorial `Student` , a entidade terá referências a outras entidades.</span><span class="sxs-lookup"><span data-stu-id="ac288-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="ac288-271">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="ac288-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="ac288-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ac288-272">Startup.cs</span></span>

<span data-ttu-id="ac288-273">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ac288-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ac288-274">Serviços como o `SchoolContext` são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="ac288-275">Os componentes que exigem esses serviços, como :::no-loc(Razor)::: páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="ac288-275">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="ac288-276">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ac288-277">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="ac288-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-279">As linhas realçadas a seguir foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="ac288-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-281">Verifique o código adicionado pelas chamadas scaffolder `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="ac288-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="ac288-282">Consulte [usar o SQLite para desenvolvimento, SQL Server para produção](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) para obter informações sobre como usar um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="ac288-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="ac288-283">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="ac288-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ac288-284">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="ac288-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="ac288-285">Adicionar o filtro de exceção de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-285">Add the database exception filter</span></span>

<span data-ttu-id="ac288-286">Adicione <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> ao `ConfigureServices` conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac288-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="ac288-288">Adicione o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="ac288-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="ac288-289">No PMC, insira o seguinte para adicionar o pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="ac288-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="ac288-291">O `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacote NuGet fornece ASP.NET Core middleware para Entity Framework Core páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="ac288-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="ac288-292">Esse middleware ajuda a detectar e diagnosticar erros com Entity Framework Core migrações.</span><span class="sxs-lookup"><span data-stu-id="ac288-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="ac288-293">O `AddDatabaseDeveloperPageExceptionFilter` fornece informações de erro úteis no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ac288-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="ac288-294">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-294">Create the database</span></span>

<span data-ttu-id="ac288-295">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="ac288-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="ac288-296">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="ac288-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="ac288-297">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="ac288-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="ac288-298">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="ac288-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="ac288-299">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-299">Delete the database.</span></span> <span data-ttu-id="ac288-300">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="ac288-300">Any existing data is lost.</span></span>
* <span data-ttu-id="ac288-301">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-301">Change the data model.</span></span> <span data-ttu-id="ac288-302">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="ac288-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="ac288-303">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-303">Run the app.</span></span>
* <span data-ttu-id="ac288-304">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="ac288-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="ac288-305">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="ac288-306">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="ac288-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="ac288-307">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="ac288-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="ac288-308">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="ac288-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="ac288-309">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="ac288-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ac288-310">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac288-310">Test the app</span></span>

* <span data-ttu-id="ac288-311">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-311">Run the app.</span></span>
* <span data-ttu-id="ac288-312">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="ac288-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ac288-313">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="ac288-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ac288-314">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-314">Seed the database</span></span>

<span data-ttu-id="ac288-315">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="ac288-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="ac288-316">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="ac288-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="ac288-317">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="ac288-318">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="ac288-319">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="ac288-320">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="ac288-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="ac288-321">Em *Program.cs* , substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="ac288-321">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-323">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="ac288-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="ac288-324">Responda com `Y` para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-326">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="ac288-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="ac288-327">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-327">Restart the app.</span></span>
* <span data-ttu-id="ac288-328">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="ac288-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="ac288-329">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-331">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac288-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="ac288-332">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="ac288-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="ac288-333">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="ac288-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="ac288-334">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="ac288-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="ac288-335">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="ac288-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="ac288-336">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-338">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="ac288-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="ac288-339">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="ac288-340">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="ac288-340">Asynchronous code</span></span>

<span data-ttu-id="ac288-341">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="ac288-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ac288-342">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="ac288-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ac288-343">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="ac288-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ac288-344">Com o código síncrono, muitos threads podem ser ligados enquanto não estão trabalhando porque estão aguardando a conclusão de e/s.</span><span class="sxs-lookup"><span data-stu-id="ac288-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ac288-345">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="ac288-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ac288-346">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="ac288-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="ac288-347">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ac288-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ac288-348">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="ac288-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ac288-349">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="ac288-350">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="ac288-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ac288-351">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="ac288-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ac288-352">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="ac288-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="ac288-353">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="ac288-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="ac288-354">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="ac288-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ac288-355">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ac288-356">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="ac288-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ac288-357">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="ac288-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ac288-358">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="ac288-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ac288-359">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="ac288-360">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac288-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ac288-361">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="ac288-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ac288-362">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="ac288-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ac288-363">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="ac288-364">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ac288-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="ac288-365">Considerações sobre o desempenho</span><span class="sxs-lookup"><span data-stu-id="ac288-365">Performance considerations</span></span>

<span data-ttu-id="ac288-366">Em geral, uma página da Web não deve carregar um número arbitrário de linhas.</span><span class="sxs-lookup"><span data-stu-id="ac288-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="ac288-367">Uma consulta deve usar a paginação ou uma abordagem de limitação.</span><span class="sxs-lookup"><span data-stu-id="ac288-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="ac288-368">Por exemplo, a consulta anterior poderia usar `Take` para limitar as linhas retornadas:</span><span class="sxs-lookup"><span data-stu-id="ac288-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ac288-369">Enumerar uma tabela grande em uma exibição pode retornar uma resposta HTTP 200 parcialmente construída se uma exceção de banco de dados ocorrer parcialmente por meio da enumeração.</span><span class="sxs-lookup"><span data-stu-id="ac288-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="ac288-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> o padrão é 1024.</span><span class="sxs-lookup"><span data-stu-id="ac288-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="ac288-371">Os seguintes conjuntos de código `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="ac288-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="ac288-372">A paginação será abordada posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac288-373">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ac288-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ac288-374">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="ac288-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ac288-375">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="ac288-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="ac288-376">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="ac288-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ac288-377">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="ac288-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ac288-378">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="ac288-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="ac288-379">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="ac288-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="ac288-380">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="ac288-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ac288-381">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ac288-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ac288-382">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ac288-382">Prerequisites</span></span>

* <span data-ttu-id="ac288-383">Se você for novo em :::no-loc(Razor)::: páginas, consulte a série de tutoriais [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="ac288-383">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="ac288-386">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-386">Database engines</span></span>

<span data-ttu-id="ac288-387">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="ac288-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="ac288-388">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ac288-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="ac288-389">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="ac288-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ac288-390">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="ac288-390">Troubleshooting</span></span>

<span data-ttu-id="ac288-391">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ac288-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ac288-392">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="ac288-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="ac288-393">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="ac288-393">The sample app</span></span>

<span data-ttu-id="ac288-394">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="ac288-395">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="ac288-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ac288-396">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-396">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="ac288-399">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="ac288-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="ac288-400">O foco do tutorial está em como usar o EF Core, não em como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="ac288-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="ac288-401">Siga o link na parte superior da página para obter o código-fonte do projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="ac288-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="ac288-402">A pasta *cu30* tem o código para a versão ASP.NET Core 3.0 do tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="ac288-403">Os arquivos que refletem o estado do código para os tutoriais 1-7 podem ser encontrados na pasta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="ac288-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-405">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="ac288-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="ac288-406">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-406">Build the project.</span></span>
* <span data-ttu-id="ac288-407">No PMC (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ac288-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="ac288-408">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-410">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="ac288-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="ac288-411">Exclua *ContosoUniversity.csproj* e altere o nome de *ContosoUniversitySQLite.csproj* para *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="ac288-411">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="ac288-412">No *Program.cs* , comente `#define Startup` que `StartupSQLite` é usado.</span><span class="sxs-lookup"><span data-stu-id="ac288-412">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="ac288-413">Exclua *appSettings.json* e altere o nome de *appSettingsSQLite.json* para *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ac288-413">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="ac288-414">Exclua a pasta *Migrations* e altere o nome de *MigrationsSQL* para *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="ac288-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="ac288-415">Faça uma pesquisa global para `#if SQLiteVersion` e remova `#if SQLiteVersion` e a `#endif` instrução associada.</span><span class="sxs-lookup"><span data-stu-id="ac288-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="ac288-416">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-416">Build the project.</span></span>
* <span data-ttu-id="ac288-417">Em um prompt de comando na pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ac288-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="ac288-418">Em sua ferramenta do SQLite, execute a seguinte instrução SQL:</span><span class="sxs-lookup"><span data-stu-id="ac288-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="ac288-419">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="ac288-420">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ac288-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-422">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ac288-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ac288-423">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ac288-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ac288-424">Nomeie o projeto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="ac288-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="ac288-425">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="ac288-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="ac288-426">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="ac288-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-428">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="ac288-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="ac288-429">Execute os seguintes comandos para criar um :::no-loc(Razor)::: projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="ac288-429">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ac288-430">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="ac288-430">Set up the site style</span></span>

<span data-ttu-id="ac288-431">Configure o cabeçalho, o rodapé e o menu do site atualizando *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ac288-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="ac288-432">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="ac288-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ac288-433">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="ac288-433">There are three occurrences.</span></span>

* <span data-ttu-id="ac288-434">Exclua as entradas de menu **Início** e **Privacidade** , então adicione as entradas para **Sobre** , **Alunos** , **Cursos** , **Instrutores** e **Departamentos**.</span><span class="sxs-lookup"><span data-stu-id="ac288-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="ac288-435">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="ac288-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="ac288-436">Em *Pages/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET Core pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ac288-436">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="ac288-437">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="ac288-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="ac288-438">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-438">The data model</span></span>

<span data-ttu-id="ac288-439">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="ac288-439">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="ac288-441">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="ac288-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="ac288-442">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="ac288-442">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="ac288-444">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="ac288-445">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="ac288-446">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="ac288-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="ac288-447">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="ac288-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ac288-448">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="ac288-449">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="ac288-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="ac288-450">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ac288-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ac288-451">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="ac288-452">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="ac288-453">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="ac288-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="ac288-454">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="ac288-455">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="ac288-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="ac288-456">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="ac288-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="ac288-457">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="ac288-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="ac288-458">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="ac288-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="ac288-459">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="ac288-460">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac288-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="ac288-461">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="ac288-461">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="ac288-463">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="ac288-464">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="ac288-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="ac288-465">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="ac288-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="ac288-466">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="ac288-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="ac288-467">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="ac288-468">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="ac288-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ac288-469">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="ac288-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="ac288-470">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="ac288-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ac288-471">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ac288-472">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="ac288-473">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ac288-474">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ac288-475">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ac288-476">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ac288-477">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ac288-478">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ac288-479">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="ac288-479">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="ac288-481">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="ac288-482">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="ac288-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ac288-483">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ac288-484">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="ac288-485">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="ac288-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="ac288-486">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="ac288-486">Scaffold Student pages</span></span>

<span data-ttu-id="ac288-487">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="ac288-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="ac288-488">Uma classe *contexto* do EF Core.</span><span class="sxs-lookup"><span data-stu-id="ac288-488">An EF Core *context* class.</span></span> <span data-ttu-id="ac288-489">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="ac288-490">Ele deriva da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="ac288-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="ac288-491">:::no-loc(Razor)::: páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-491">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-493">Crie uma pasta *Alunos* na pasta *Páginas*.</span><span class="sxs-lookup"><span data-stu-id="ac288-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="ac288-494">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="ac288-494">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ac288-495">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-495">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="ac288-496">Na caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="ac288-496">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="ac288-497">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="ac288-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="ac288-498">Na linha **Classe de contexto de dados** , selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="ac288-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="ac288-499">Altere o nome do contexto de dados de *ContosoUniversity.Models.ContosoUniversityContext* para *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="ac288-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="ac288-500">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-500">Select **Add**.</span></span>

<span data-ttu-id="ac288-501">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="ac288-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-503">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="ac288-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="ac288-504">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="ac288-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="ac288-505">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ac288-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="ac288-506">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ac288-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="ac288-507">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="ac288-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="ac288-508">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="ac288-509">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="ac288-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="ac288-510">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="ac288-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="ac288-511">Se você tiver um problema com a etapa anterior, compile o projeto e repita a etapa de scaffold.</span><span class="sxs-lookup"><span data-stu-id="ac288-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="ac288-512">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="ac288-512">The scaffolding process:</span></span>

* <span data-ttu-id="ac288-513">Cria :::no-loc(Razor)::: páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="ac288-513">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="ac288-514">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-515">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-516">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-517">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="ac288-518">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="ac288-519">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac288-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="ac288-520">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac288-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="ac288-521">Adiciona uma cadeia de conexão de banco de dados a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="ac288-521">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="ac288-522">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-524">O *:::no-loc(appsettings.json):::* arquivo especifica a cadeia de conexão [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="ac288-524">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="ac288-525">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="ac288-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ac288-526">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-528">Altere a cadeia de conexão para apontar para um arquivo de banco de dados SQLite chamado *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="ac288-528">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="ac288-529">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-529">Update the database context class</span></span>

<span data-ttu-id="ac288-530">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="ac288-531">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="ac288-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ac288-532">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ac288-533">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ac288-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ac288-534">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="ac288-535">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="ac288-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ac288-536">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="ac288-536">In EF Core terminology:</span></span>

* <span data-ttu-id="ac288-537">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="ac288-538">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="ac288-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ac288-539">Como um conjunto de entidades contém várias entidades, as propriedades DBSet devem ser nomes no plural.</span><span class="sxs-lookup"><span data-stu-id="ac288-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="ac288-540">Como a ferramenta scaffolding criou um`Student` DBSet, essa etapa o altera para o `Students` no plural.</span><span class="sxs-lookup"><span data-stu-id="ac288-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="ac288-541">Para fazer com que o :::no-loc(Razor)::: código das páginas corresponda ao novo nome de DBSet, faça uma alteração global em todo o projeto de `_context.Student` para `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="ac288-541">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="ac288-542">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="ac288-542">There are 8 occurrences.</span></span>

<span data-ttu-id="ac288-543">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="ac288-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="ac288-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ac288-544">Startup.cs</span></span>

<span data-ttu-id="ac288-545">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ac288-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ac288-546">Serviços (como o contexto de banco de dados do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="ac288-547">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="ac288-547">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ac288-548">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ac288-549">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="ac288-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-551">Em `ConfigureServices`, as linhas destacadas foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="ac288-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-553">Em `ConfigureServices`, verifique se o código adicionado pelo scaffolder chama `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="ac288-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="ac288-554">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="ac288-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ac288-555">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="ac288-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="ac288-556">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-556">Create the database</span></span>

<span data-ttu-id="ac288-557">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="ac288-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="ac288-558">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="ac288-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="ac288-559">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="ac288-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="ac288-560">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="ac288-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="ac288-561">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-561">Delete the database.</span></span> <span data-ttu-id="ac288-562">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="ac288-562">Any existing data is lost.</span></span>
* <span data-ttu-id="ac288-563">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-563">Change the data model.</span></span> <span data-ttu-id="ac288-564">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="ac288-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="ac288-565">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-565">Run the app.</span></span>
* <span data-ttu-id="ac288-566">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="ac288-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="ac288-567">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="ac288-568">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="ac288-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="ac288-569">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="ac288-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="ac288-570">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="ac288-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="ac288-571">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="ac288-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ac288-572">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac288-572">Test the app</span></span>

* <span data-ttu-id="ac288-573">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-573">Run the app.</span></span>
* <span data-ttu-id="ac288-574">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="ac288-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ac288-575">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="ac288-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ac288-576">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-576">Seed the database</span></span>

<span data-ttu-id="ac288-577">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="ac288-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="ac288-578">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="ac288-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="ac288-579">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="ac288-580">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="ac288-581">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="ac288-582">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="ac288-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="ac288-583">Em *Program.cs* , substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="ac288-583">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-585">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="ac288-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-587">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="ac288-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="ac288-588">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-588">Restart the app.</span></span>

* <span data-ttu-id="ac288-589">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="ac288-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="ac288-590">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-592">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac288-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="ac288-593">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="ac288-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="ac288-594">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="ac288-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="ac288-595">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="ac288-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="ac288-596">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="ac288-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="ac288-597">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-599">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="ac288-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="ac288-600">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="ac288-601">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="ac288-601">Asynchronous code</span></span>

<span data-ttu-id="ac288-602">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="ac288-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ac288-603">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="ac288-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ac288-604">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="ac288-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ac288-605">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="ac288-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ac288-606">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="ac288-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ac288-607">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="ac288-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="ac288-608">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ac288-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ac288-609">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="ac288-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ac288-610">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="ac288-611">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="ac288-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ac288-612">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="ac288-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ac288-613">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="ac288-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="ac288-614">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="ac288-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="ac288-615">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="ac288-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ac288-616">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ac288-617">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="ac288-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ac288-618">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="ac288-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ac288-619">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="ac288-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ac288-620">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="ac288-621">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac288-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ac288-622">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="ac288-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ac288-623">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="ac288-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ac288-624">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="ac288-625">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ac288-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac288-626">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ac288-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ac288-627">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="ac288-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac288-628">O aplicativo Web de exemplo Contoso University demonstra como criar um :::no-loc(Razor)::: aplicativo de páginas ASP.NET Core usando Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="ac288-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="ac288-629">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="ac288-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ac288-630">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="ac288-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ac288-631">Esta página é a primeira de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="ac288-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="ac288-632">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="ac288-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ac288-633">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ac288-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ac288-634">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ac288-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="ac288-637">Familiaridade com [ :::no-loc(Razor)::: páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="ac288-637">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="ac288-638">Os novos programadores devem concluir a [introdução às :::no-loc(Razor)::: páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta série.</span><span class="sxs-lookup"><span data-stu-id="ac288-638">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ac288-639">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="ac288-639">Troubleshooting</span></span>

<span data-ttu-id="ac288-640">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ac288-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ac288-641">Uma boa maneira de obter ajuda é postando uma pergunta no [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="ac288-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="ac288-642">O aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="ac288-642">The Contoso University web app</span></span>

<span data-ttu-id="ac288-643">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="ac288-644">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="ac288-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ac288-645">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-645">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

<span data-ttu-id="ac288-648">O estilo de interface do usuário deste site é próximo ao que é gerado pelos modelos internos.</span><span class="sxs-lookup"><span data-stu-id="ac288-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="ac288-649">O foco do tutorial está em EF Core com :::no-loc(Razor)::: páginas, não na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="ac288-649">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="ac288-650">Criar o :::no-loc(Razor)::: aplicativo Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="ac288-650">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-652">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ac288-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ac288-653">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac288-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="ac288-654">Nomeie o projeto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="ac288-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="ac288-655">É importante nomear o projeto *ContosoUniversity* para que os namespaces sejam correspondentes quando o código for copiado/colado.</span><span class="sxs-lookup"><span data-stu-id="ac288-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="ac288-656">Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="ac288-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="ac288-657">Para imagens das etapas anteriores, consulte [criar um :::no-loc(Razor)::: aplicativo Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="ac288-657">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="ac288-658">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ac288-660">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="ac288-660">Set up the site style</span></span>

<span data-ttu-id="ac288-661">Algumas alterações configuram o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="ac288-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="ac288-662">Atualize *Pages/Shared/_Layout.cshtml* com as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="ac288-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="ac288-663">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="ac288-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ac288-664">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="ac288-664">There are three occurrences.</span></span>

* <span data-ttu-id="ac288-665">Adicione entradas de menu para **Alunos** , **Cursos** , **Instrutores** e **Departamentos** e exclua a entrada de menu **Contato**.</span><span class="sxs-lookup"><span data-stu-id="ac288-665">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="ac288-666">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="ac288-666">The changes are highlighted.</span></span> <span data-ttu-id="ac288-667">(Toda a marcação *não* é exibida.)</span><span class="sxs-lookup"><span data-stu-id="ac288-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="ac288-668">Em *Pages/Index.cshtml* , substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ac288-668">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="ac288-669">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="ac288-669">Create the data model</span></span>

<span data-ttu-id="ac288-670">Crie classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="ac288-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="ac288-671">Comece com as três seguintes entidades:</span><span class="sxs-lookup"><span data-stu-id="ac288-671">Start with the following three entities:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="ac288-673">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="ac288-674">Há uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="ac288-675">Um aluno pode se registrar em qualquer quantidade de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac288-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="ac288-676">Um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="ac288-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="ac288-677">Nas seções a seguir, é criada uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="ac288-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="ac288-678">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="ac288-678">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="ac288-680">Crie uma pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="ac288-680">Create a *Models* folder.</span></span> <span data-ttu-id="ac288-681">Na pasta *Models* , crie um arquivo de classe chamado *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="ac288-682">A propriedade `ID` se torna a coluna de chave primária da tabela de BD (banco de dados) que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="ac288-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="ac288-683">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="ac288-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ac288-684">Em `classnameID`, `classname` é o nome da classe.</span><span class="sxs-lookup"><span data-stu-id="ac288-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="ac288-685">A chave primária alternativa reconhecida automaticamente é `StudentID` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ac288-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="ac288-686">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ac288-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ac288-687">As propriedades de navegação vinculam-se a outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="ac288-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="ac288-688">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazena todas as entidades `Enrollment` relacionadas a essa `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="ac288-689">Por exemplo, se uma linha Aluno no BD tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="ac288-690">Uma linha `Enrollment` relacionada é uma linha que contém o valor de chave primária do aluno na coluna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="ac288-691">Por exemplo, suponha que o aluno com ID=1 tenha duas linhas na tabela `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="ac288-692">A tabela `Enrollment` tem duas linhas com `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="ac288-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="ac288-693">`StudentID` é uma chave estrangeira na tabela `Enrollment` que especifica o aluno na tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="ac288-694">Se uma propriedade de navegação puder armazenar várias entidades, a propriedade de navegação deverá ser um tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="ac288-695">`ICollection<T>` pode ser especificado ou um tipo como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="ac288-696">Quando `ICollection<T>` é usado, o EF Core cria uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac288-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="ac288-697">As propriedades de navegação que armazenam várias entidades são provenientes de relações muitos para muitos e um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="ac288-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="ac288-698">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="ac288-698">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="ac288-700">Na pasta *Models* , crie *Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="ac288-701">A propriedade `EnrollmentID` é a chave primária.</span><span class="sxs-lookup"><span data-stu-id="ac288-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="ac288-702">Essa entidade usa o padrão `classnameID` em vez de `ID` como a entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="ac288-703">Normalmente, os desenvolvedores escolhem um padrão e o usam em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="ac288-704">Em um tutorial posterior, o uso de uma ID sem nome de classe é mostrado para facilitar a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="ac288-705">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="ac288-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ac288-706">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="ac288-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="ac288-707">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="ac288-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ac288-708">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ac288-709">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="ac288-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="ac288-710">A entidade `Student` é distinta da propriedade de navegação `Student.Enrollments`, que contém várias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="ac288-711">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ac288-712">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ac288-713">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ac288-714">Por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ac288-715">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ac288-716">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac288-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="ac288-717">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="ac288-717">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="ac288-719">Na pasta *Models* , crie *Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="ac288-720">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="ac288-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ac288-721">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ac288-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ac288-722">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="ac288-723">Gere um modelo de aluno por scaffold</span><span class="sxs-lookup"><span data-stu-id="ac288-723">Scaffold the student model</span></span>

<span data-ttu-id="ac288-724">Nesta seção, é feito o scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="ac288-725">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="ac288-726">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="ac288-726">Build the project.</span></span>
* <span data-ttu-id="ac288-727">Crie a pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ac288-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac288-729">No **Gerenciador de Soluções** , clique com o botão direito do mouse na pasta *Pages/Students* > **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="ac288-729">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ac288-730">Na caixa de diálogo **Adicionar Scaffold** , selecione **:::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-730">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="ac288-731">Conclua a caixa de diálogo **Adicionar :::no-loc(Razor)::: páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="ac288-731">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="ac288-732">Na lista suspensa **classe Modelo** , selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="ac288-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="ac288-733">Na linha **Classe de contexto de dados** , selecione o sinal de (mais) **+** e altere o nome gerado para **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="ac288-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="ac288-734">Na lista suspensa **Classe de contexto de dados** , selecione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="ac288-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="ac288-735">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ac288-735">Select **Add**.</span></span>

![Caixa de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="ac288-737">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se tiver problemas na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="ac288-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac288-739">Execute os comandos a seguir para fazer scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="ac288-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="ac288-740">O processo de scaffold criou e alterou os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="ac288-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="ac288-741">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="ac288-741">Files created</span></span>

* <span data-ttu-id="ac288-742">*Pages/Students* Criar, Excluir, Detalhes, Editar, Índice.</span><span class="sxs-lookup"><span data-stu-id="ac288-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="ac288-743">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="ac288-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="ac288-744">Atualizações de arquivo</span><span class="sxs-lookup"><span data-stu-id="ac288-744">File updates</span></span>

* <span data-ttu-id="ac288-745">*Startup.cs* : alterações a esse arquivo serão detalhadas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="ac288-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="ac288-746">*:::no-loc(appsettings.json):::* : A cadeia de conexão usada para se conectar a um banco de dados local é adicionada.</span><span class="sxs-lookup"><span data-stu-id="ac288-746">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="ac288-747">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="ac288-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="ac288-748">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ac288-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ac288-749">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="ac288-750">Os componentes que exigem esses serviços (como :::no-loc(Razor)::: páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="ac288-750">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ac288-751">O código de construtor que obtém uma instância de contexto do BD é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="ac288-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ac288-752">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="ac288-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="ac288-753">Examine o método `ConfigureServices` em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac288-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="ac288-754">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="ac288-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="ac288-755">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="ac288-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ac288-756">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *:::no-loc(appsettings.json):::* arquivo.</span><span class="sxs-lookup"><span data-stu-id="ac288-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="ac288-757">Atualizar o principal</span><span class="sxs-lookup"><span data-stu-id="ac288-757">Update main</span></span>

<span data-ttu-id="ac288-758">Em *Program.cs* , modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ac288-758">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="ac288-759">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="ac288-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="ac288-760">Chame o [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="ac288-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="ac288-761">Descarte o contexto quando o método `EnsureCreated` for concluído.</span><span class="sxs-lookup"><span data-stu-id="ac288-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="ac288-762">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="ac288-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="ac288-763">`EnsureCreated` garante que o banco de dados do contexto exista.</span><span class="sxs-lookup"><span data-stu-id="ac288-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="ac288-764">Se ele existir, nenhuma ação será realizada.</span><span class="sxs-lookup"><span data-stu-id="ac288-764">If it exists, no action is taken.</span></span> <span data-ttu-id="ac288-765">Se ele não existir, o banco de dados e todos os seus esquemas serão criados.</span><span class="sxs-lookup"><span data-stu-id="ac288-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="ac288-766">`EnsureCreated` não usa migrações para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="ac288-767">Um banco de dados criado com `EnsureCreated` não pode ser atualizado posteriormente usando migrações.</span><span class="sxs-lookup"><span data-stu-id="ac288-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="ac288-768">`EnsureCreated` é chamado na inicialização do aplicativo, que permite que o seguinte fluxo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="ac288-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="ac288-769">Exclua o BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-769">Delete the DB.</span></span>
* <span data-ttu-id="ac288-770">Altere o esquema de BD (por exemplo, adicione um campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="ac288-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="ac288-771">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac288-771">Run the app.</span></span>
* <span data-ttu-id="ac288-772">`EnsureCreated` cria um BD com a coluna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="ac288-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="ac288-773">`EnsureCreated` é conveniente no início do desenvolvimento quando o esquema está evoluindo rapidamente.</span><span class="sxs-lookup"><span data-stu-id="ac288-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="ac288-774">Mais tarde, no tutorial do banco de dados, é excluído e as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="ac288-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ac288-775">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac288-775">Test the app</span></span>

<span data-ttu-id="ac288-776">Execute o aplicativo e aceite a :::no-loc(cookie)::: política.</span><span class="sxs-lookup"><span data-stu-id="ac288-776">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="ac288-777">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="ac288-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="ac288-778">Você pode ler sobre a :::no-loc(cookie)::: política no [suporte de regulamento geral sobre a proteção de dados da UE (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ac288-778">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="ac288-779">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="ac288-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ac288-780">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="ac288-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="ac288-781">Examine o contexto de BD SchoolContext</span><span class="sxs-lookup"><span data-stu-id="ac288-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="ac288-782">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="ac288-783">O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="ac288-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ac288-784">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ac288-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ac288-785">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ac288-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ac288-786">Atualize *SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="ac288-787">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="ac288-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ac288-788">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="ac288-788">In EF Core terminology:</span></span>

* <span data-ttu-id="ac288-789">Um conjunto de entidades normalmente corresponde a uma tabela de BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="ac288-790">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="ac288-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ac288-791">`DbSet<Enrollment>` e `DbSet<Course>` podem ser omitidos.</span><span class="sxs-lookup"><span data-stu-id="ac288-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="ac288-792">O EF Core inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="ac288-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="ac288-793">Para este tutorial, mantenha `DbSet<Enrollment>` e `DbSet<Course>` no `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ac288-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="ac288-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ac288-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ac288-795">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="ac288-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="ac288-796">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="ac288-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ac288-797">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="ac288-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ac288-798">Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="ac288-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="ac288-799">Adicionar um código para inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="ac288-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="ac288-800">O EF Core cria um BD vazio.</span><span class="sxs-lookup"><span data-stu-id="ac288-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="ac288-801">Nesta seção, um método `Initialize` é escrito para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="ac288-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="ac288-802">Na pasta *Dados* , crie um novo arquivo de classe chamado *DbInitializer.cs* e adicione o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ac288-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="ac288-803">Observação: o código anterior usa `Models` para o namespace ( `namespace ContosoUniversity.Models` ) em vez de `Data` .</span><span class="sxs-lookup"><span data-stu-id="ac288-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="ac288-804">`Models` é consistente com o código gerado pelo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="ac288-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="ac288-805">Para saber mais, confira [este problema de scaffolding do GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="ac288-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="ac288-806">O código verifica se há alunos no BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="ac288-807">Se não houver nenhum aluno no BD, o BD será inicializado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="ac288-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="ac288-808">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="ac288-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="ac288-809">O método `EnsureCreated` cria o BD automaticamente para o contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="ac288-810">Se o BD existir, `EnsureCreated` retornará sem modificar o BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="ac288-811">Em *Program.cs* , modifique o método `Main` para chamar `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="ac288-811">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="ac288-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac288-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac288-813">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="ac288-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac288-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac288-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac288-815">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="ac288-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="ac288-816">Exibir o BD</span><span class="sxs-lookup"><span data-stu-id="ac288-816">View the DB</span></span>

<span data-ttu-id="ac288-817">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="ac288-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="ac288-818">Assim, o nome do banco de dados será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="ac288-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="ac288-819">O GUID será diferente para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="ac288-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="ac288-820">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac288-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="ac288-821">No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="ac288-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="ac288-822">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="ac288-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="ac288-823">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="ac288-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="ac288-824">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="ac288-824">Asynchronous code</span></span>

<span data-ttu-id="ac288-825">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="ac288-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ac288-826">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="ac288-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ac288-827">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="ac288-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ac288-828">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="ac288-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ac288-829">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="ac288-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ac288-830">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="ac288-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="ac288-831">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ac288-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ac288-832">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="ac288-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ac288-833">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="ac288-834">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="ac288-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ac288-835">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="ac288-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ac288-836">Criar automaticamente o objeto [Task](/dotnet/api/system.threading.tasks.task) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="ac288-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="ac288-837">Para obter mais informações, consulte [Tipo de retorno de Tarefa](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="ac288-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="ac288-838">O tipo de retorno implícito `Task` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="ac288-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="ac288-839">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="ac288-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ac288-840">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ac288-841">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="ac288-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ac288-842">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="ac288-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ac288-843">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="ac288-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ac288-844">Somente instruções que fazem com que consultas ou comandos sejam enviados ao BD são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac288-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="ac288-845">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac288-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ac288-846">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="ac288-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ac288-847">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="ac288-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ac288-848">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamam métodos do EF Core que enviam consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="ac288-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="ac288-849">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ac288-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="ac288-850">No próximo tutorial, as operações CRUD (criar, ler, atualizar e excluir) básicas são examinadas.</span><span class="sxs-lookup"><span data-stu-id="ac288-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="ac288-851">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ac288-851">Additional resources</span></span>

* [<span data-ttu-id="ac288-852">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="ac288-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="ac288-853">Próximo</span><span class="sxs-lookup"><span data-stu-id="ac288-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
