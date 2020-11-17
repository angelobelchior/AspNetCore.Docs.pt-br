---
title: Tutorial parte 5, aplicar migrações ao exemplo de Universidade da contoso
description: Parte 5 da série de tutoriais da Contoso University. Use o recurso de migrações EF Core para gerenciar alterações no modelo de dados em um aplicativo ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: contperfq2
ms.date: 11/13/2020
ms.topic: tutorial
no-loc:
- appsettings.json
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: ab5be222416e61fcff90c5130ca91ad4a2a5c9b0
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673998"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="0910d-104">Tutorial: parte 5, aplicar migrações ao exemplo da Contoso University</span><span class="sxs-lookup"><span data-stu-id="0910d-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="0910d-105">Neste tutorial, você começa usando o recurso de migrações do EF Core para o gerenciamento de alterações do modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="0910d-106">Em tutoriais seguintes, você adicionará mais migrações conforme você alterar o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="0910d-107">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="0910d-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0910d-108">Aprenderá sobre migrações</span><span class="sxs-lookup"><span data-stu-id="0910d-108">Learn about migrations</span></span>
> * <span data-ttu-id="0910d-109">Criar uma migração inicial</span><span class="sxs-lookup"><span data-stu-id="0910d-109">Create an initial migration</span></span>
> * <span data-ttu-id="0910d-110">Examinará os métodos Up e Down</span><span class="sxs-lookup"><span data-stu-id="0910d-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="0910d-111">Aprenderá sobre o instantâneo do modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0910d-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="0910d-112">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="0910d-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0910d-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0910d-113">Prerequisites</span></span>

* [<span data-ttu-id="0910d-114">Classificação, filtragem e paginação</span><span class="sxs-lookup"><span data-stu-id="0910d-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="0910d-115">Sobre migrações</span><span class="sxs-lookup"><span data-stu-id="0910d-115">About migrations</span></span>

<span data-ttu-id="0910d-116">Quando você desenvolve um novo aplicativo, o modelo de dados é alterado com frequência e, sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="0910d-117">Você começou estes tutoriais configurando o Entity Framework para criar o banco de dados, caso ele não exista.</span><span class="sxs-lookup"><span data-stu-id="0910d-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="0910d-118">Em seguida, sempre que você alterar o modelo de dados – adicionar, remover, alterar classes de entidade ou alterar a classe DbContext –, poderá excluir o banco de dados e o EF criará um novo que corresponde ao modelo e o propagará com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="0910d-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="0910d-119">Esse método de manter o banco de dados em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção.</span><span class="sxs-lookup"><span data-stu-id="0910d-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="0910d-120">Quando o aplicativo é executado em produção, ele normalmente armazena os dados que você deseja manter, e você não quer perder tudo sempre que fizer uma alteração, como a adição de uma nova coluna.</span><span class="sxs-lookup"><span data-stu-id="0910d-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="0910d-121">O recurso Migrações do EF Core resolve esse problema, permitindo que o EF atualize o esquema de banco de dados em vez de criar um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="0910d-122">Para trabalhar com migrações, você pode usar o **console do Gerenciador de pacotes** (PMC) ou a CLI.</span><span class="sxs-lookup"><span data-stu-id="0910d-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="0910d-123">Esses tutoriais mostram como usar comandos da CLI.</span><span class="sxs-lookup"><span data-stu-id="0910d-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="0910d-124">Encontre informações sobre o PMC no [final deste tutorial](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0910d-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="0910d-125">Remover o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0910d-125">Drop the database</span></span>

<span data-ttu-id="0910d-126">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-126">Delete the database.</span></span> <span data-ttu-id="0910d-127">Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop` da CLI:</span><span class="sxs-lookup"><span data-stu-id="0910d-127">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

<span data-ttu-id="0910d-128">A seção a seguir explica como executar comandos da CLI.</span><span class="sxs-lookup"><span data-stu-id="0910d-128">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="0910d-129">Criar uma migração inicial</span><span class="sxs-lookup"><span data-stu-id="0910d-129">Create an initial migration</span></span>

<span data-ttu-id="0910d-130">Salve as alterações e compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="0910d-130">Save your changes and build the project.</span></span> <span data-ttu-id="0910d-131">Em seguida, abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="0910d-131">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="0910d-132">Esta é uma maneira rápida de fazer isso:</span><span class="sxs-lookup"><span data-stu-id="0910d-132">Here's a quick way to do that:</span></span>

* <span data-ttu-id="0910d-133">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Abrir Pasta no Explorador de Arquivos** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="0910d-133">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Abrir no item de menu do Explorador de Arquivos](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="0910d-135">Insira "cmd" na barra de endereços e pressione Enter.</span><span class="sxs-lookup"><span data-stu-id="0910d-135">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Abrir a janela Comando](migrations/_static/open-command-window.png)

<span data-ttu-id="0910d-137">Insira o seguinte comando na janela de comando:</span><span class="sxs-lookup"><span data-stu-id="0910d-137">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="0910d-138">`dotnet tool install --global dotnet-ef` o é instalado `dotnet ef` como uma [ferramenta global](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="0910d-138">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="0910d-139">Nos comandos anteriores, uma saída semelhante à seguinte é exibida:</span><span class="sxs-lookup"><span data-stu-id="0910d-139">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="0910d-140">Se você vir uma mensagem de erro "*não é possível acessar o arquivo... ContosoUniversity.dll porque ele está sendo usado por outro processo.*", localize o ícone de IIS Express na bandeja do sistema do Windows e clique com o botão direito do mouse nele e clique em **ContosoUniversity > parar site**.</span><span class="sxs-lookup"><span data-stu-id="0910d-140">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="0910d-141">Examinará os métodos Up e Down</span><span class="sxs-lookup"><span data-stu-id="0910d-141">Examine Up and Down methods</span></span>

<span data-ttu-id="0910d-142">Quando você executou o comando `migrations add`, o EF gerou o código que criará o banco de dados do zero.</span><span class="sxs-lookup"><span data-stu-id="0910d-142">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="0910d-143">Esse código está na pasta *migrações* , no arquivo chamado *\<timestamp> _InitialCreate. cs*.</span><span class="sxs-lookup"><span data-stu-id="0910d-143">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="0910d-144">O método `Up` da classe `InitialCreate` cria as tabelas de banco de dados que correspondem aos conjuntos de entidades do modelo de dados, e o método `Down` exclui-as, conforme mostrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="0910d-144">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="0910d-145">As migrações chamam o método `Up` para implementar as alterações do modelo de dados para uma migração.</span><span class="sxs-lookup"><span data-stu-id="0910d-145">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="0910d-146">Quando você insere um comando para reverter a atualização, as Migrações chamam o método `Down`.</span><span class="sxs-lookup"><span data-stu-id="0910d-146">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="0910d-147">Esse código destina-se à migração inicial que foi criada quando você inseriu o comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="0910d-147">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="0910d-148">O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo e pode ser o que você desejar.</span><span class="sxs-lookup"><span data-stu-id="0910d-148">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="0910d-149">É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração.</span><span class="sxs-lookup"><span data-stu-id="0910d-149">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="0910d-150">Por exemplo, você pode nomear uma migração posterior "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="0910d-150">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="0910d-151">Se você criou a migração inicial quando o banco de dados já existia, o código de criação de banco de dados é gerado, mas ele não precisa ser executado porque o banco de dados já corresponde ao modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-151">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="0910d-152">Quando você implantar o aplicativo em outro ambiente no qual o banco de dados ainda não existe, esse código será executado para criar o banco de dados; portanto, é uma boa ideia testá-lo primeiro.</span><span class="sxs-lookup"><span data-stu-id="0910d-152">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="0910d-153">É por isso que você alterou o nome do banco de dados na cadeia de conexão anteriormente – para que as migrações possam criar um novo do zero.</span><span class="sxs-lookup"><span data-stu-id="0910d-153">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="0910d-154">O instantâneo do modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0910d-154">The data model snapshot</span></span>

<span data-ttu-id="0910d-155">As migrações criam um *instantâneo* do esquema de banco de dados atual em *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="0910d-155">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="0910d-156">Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados com o arquivo de instantâneo.</span><span class="sxs-lookup"><span data-stu-id="0910d-156">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="0910d-157">Use o comando [dotnet EF migrações remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) para remover uma migração.</span><span class="sxs-lookup"><span data-stu-id="0910d-157">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="0910d-158">`dotnet ef migrations remove` exclui a migração e garante que o instantâneo seja redefinido corretamente.</span><span class="sxs-lookup"><span data-stu-id="0910d-158">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="0910d-159">Se `dotnet ef migrations remove` o falhar, use `dotnet ef migrations remove -v` para obter mais informações sobre a falha.</span><span class="sxs-lookup"><span data-stu-id="0910d-159">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="0910d-160">Confira [Migrações do EF Core em ambientes de equipe](/ef/core/managing-schemas/migrations/teams) para obter mais informações de como o arquivo de instantâneo é usado.</span><span class="sxs-lookup"><span data-stu-id="0910d-160">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="0910d-161">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="0910d-161">Apply the migration</span></span>

<span data-ttu-id="0910d-162">Na janela Comando, insira o comando a seguir para criar o banco de dados e tabelas nele.</span><span class="sxs-lookup"><span data-stu-id="0910d-162">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0910d-163">A saída do comando é semelhante ao comando `migrations add`, exceto que os logs para os comandos SQL que configuram o banco de dados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="0910d-163">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="0910d-164">A maioria dos logs é omitida na seguinte saída de exemplo.</span><span class="sxs-lookup"><span data-stu-id="0910d-164">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="0910d-165">Se você preferir não ver esse nível de detalhe em mensagens de log, altere o nível de log no arquivo *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="0910d-165">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="0910d-166">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="0910d-166">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="0910d-167">Use o **Pesquisador de Objetos do SQL Server** para inspecionar o banco de dados como você fez no primeiro tutorial.</span><span class="sxs-lookup"><span data-stu-id="0910d-167">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="0910d-168">Você observará a adição de uma tabela \_\_EFMigrationsHistory que controla quais migrações foram aplicadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-168">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="0910d-169">Exiba os dados dessa tabela e você verá uma linha para a primeira migração.</span><span class="sxs-lookup"><span data-stu-id="0910d-169">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="0910d-170">(O último log no exemplo de saída da CLI anterior mostra a instrução INSERT que cria essa linha.)</span><span class="sxs-lookup"><span data-stu-id="0910d-170">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="0910d-171">Execute o aplicativo para verificar se tudo ainda funciona como antes.</span><span class="sxs-lookup"><span data-stu-id="0910d-171">Run the application to verify that everything still works the same as before.</span></span>

![Página Índice de Alunos](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="0910d-173">Comparar CLI e PMC</span><span class="sxs-lookup"><span data-stu-id="0910d-173">Compare CLI and PMC</span></span>

<span data-ttu-id="0910d-174">As ferramentas do EF para gerenciamento de migrações estão disponíveis por meio dos comandos da CLI do .NET Core ou de cmdlets do PowerShell na janela **PMC** (Console do Gerenciador de Pacotes) do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0910d-174">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="0910d-175">Este tutorial mostra como usar a CLI, mas você poderá usar o PMC se preferir.</span><span class="sxs-lookup"><span data-stu-id="0910d-175">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="0910d-176">Os comandos do EF para os comandos do PMC estão no pacote [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="0910d-176">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="0910d-177">Esse pacote está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), portanto você não precisa adicionar uma referência de pacote se o aplicativo tem uma referência de pacote ao `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="0910d-177">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="0910d-178">**Importante:** esse não é o mesmo pacote que é instalado para a CLI com a edição do arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0910d-178">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="0910d-179">O nome deste termina com `Tools`, ao contrário do nome do pacote da CLI que termina com `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="0910d-179">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="0910d-180">Para obter mais informações sobre os comandos da CLI, consulte [CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="0910d-180">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="0910d-181">Para obter mais informações sobre os comandos do PMC, consulte [Console do Gerenciador de Pacotes (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="0910d-181">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="0910d-182">Obter o código</span><span class="sxs-lookup"><span data-stu-id="0910d-182">Get the code</span></span>

[<span data-ttu-id="0910d-183">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="0910d-183">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="0910d-184">Próxima etapa</span><span class="sxs-lookup"><span data-stu-id="0910d-184">Next step</span></span>

<span data-ttu-id="0910d-185">Vá para o próximo tutorial para começar a examinar tópicos mais avançados sobre a expansão do modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0910d-185">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="0910d-186">Ao longo do processo, você criará e aplicará migrações adicionais.</span><span class="sxs-lookup"><span data-stu-id="0910d-186">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0910d-187">Criar e aplicar migrações adicionais</span><span class="sxs-lookup"><span data-stu-id="0910d-187">Create and apply additional migrations</span></span>](complex-data-model.md)
