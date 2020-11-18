---
title: 'Tutorial: introdução ao EF Core em um aplicativo Web ASP.NET MVC'
description: Esta página é a primeira de uma série de tutoriais que explicam como criar o aplicativo de exemplo do EF/MVC da Contoso University "
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: data/ef-mvc/intro
ms.openlocfilehash: c0623de3c8031b6dbb518a6d25623b55a6500af5
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703729"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Tutorial: introdução ao EF Core em um aplicativo Web ASP.NET MVC

Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

O aplicativo Web de exemplo Contoso University demonstra como criar um aplicativo Web ASP.NET Core MVC usando o Entity Framework (EF) Core e o Visual Studio.

O aplicativo de exemplo é um site de uma Contoso University fictícia. Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor. Este é o primeiro de uma série de tutoriais que explicam como criar o aplicativo de exemplo da Contoso University.

## <a name="prerequisites"></a>Pré-requisitos

* Se você for novo no ASP.NET Core MVC, consulte a série de tutoriais [introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) antes de iniciar este.

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>Mecanismos de banco de dados

As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>Solucionar problemas e solucionar problemas

Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Para obter uma lista de erros comuns e como resolvê-los, consulte [a seção Solução de problemas do último tutorial da série](advanced.md#common-errors). Caso não encontre o que precisa na seção, poste uma pergunta no StackOverflow.com sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Esta é uma série de dez tutoriais, cada um se baseando no que é feito nos tutoriais anteriores. Considere a possibilidade de salvar uma cópia do projeto após a conclusão bem-sucedida de cada tutorial. Caso tenha problemas, comece novamente no tutorial anterior em vez de voltar ao início de toda a série.

## <a name="contoso-university-web-app"></a>Aplicativo Web Contoso University

O aplicativo criado nesses tutoriais é um site básico de universidade.

Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores. Aqui estão algumas das telas no aplicativo:

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Criar um aplicativo Web

1. Inicie o Visual Studio e selecione **Criar um projeto**.
1. Na caixa de diálogo **criar um novo projeto** , selecione **ASP.NET Core aplicativo Web** > **Avançar**.
1. Na caixa de diálogo **configurar seu novo projeto** , digite `ContosoUniversity` para **nome do projeto**. É importante usar esse nome exato, incluindo a capitalização, para que cada `namespace` correspondência seja feita quando o código for copiado.
1. Selecione **Criar**.
1. Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:
    1. **.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.
    1. **ASP.NET Core aplicativo Web (Model-View-Controller)**.
    1. **Create** 
       Criar ![ Caixa de diálogo novo projeto de ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>Configurar o estilo do site

Algumas alterações básicas configuram o menu, layout e home page do site.

Abra *Views/Shared/_Layout.cshtml* e faça as seguintes alterações:

* Altere cada ocorrência de `ContosoUniversity` para `Contoso University` . Há três ocorrências.
* Adicione entradas de menu **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Privacidade**.

As alterações anteriores são realçadas no código a seguir:

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

Em *views/home/index. cshtml*, substitua o conteúdo do arquivo pela seguinte marcação:

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu. O home page é exibido com guias para as páginas criadas neste tutorial.

![Home page da Contoso University](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>EF Core pacotes NuGet

Este tutorial usa o SQL Server e o pacote de provedor é [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).

O pacote de SQL Server do EF e suas dependências e `Microsoft.EntityFrameworkCore` `Microsoft.EntityFrameworkCore.Relational` fornecem suporte de tempo de execução para o EF.

Adicione o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) e o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) . No console do Gerenciador de pacotes (PMC), digite os seguintes comandos para adicionar os pacotes NuGet:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

O `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacote NuGet fornece ASP.NET Core middleware para EF Core páginas de erro. Esse middleware ajuda a detectar e diagnosticar erros com EF Core migrações.

Para obter informações sobre outros provedores de banco de dados que estão disponíveis para EF Core, consulte [provedores de banco de dados](/ef/core/providers/).

## <a name="create-the-data-model"></a>Criar o modelo de dados

As classes de entidade a seguir são criadas para este aplicativo:

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

As entidades anteriores têm as seguintes relações:

* Uma relação um-para-muitos entre `Student` as `Enrollment` entidades e. Um aluno pode ser registrado em qualquer número de cursos.
* Uma relação um-para-muitos entre `Course` as `Enrollment` entidades e. Um curso pode ter qualquer quantidade de alunos registrados.

Nas seções a seguir, uma classe é criada para cada uma dessas entidades.

### <a name="the-student-entity"></a>A entidade Student

![Diagrama da entidade Student](intro/_static/student-entity.png)

Na pasta *modelos* , crie a `Student` classe com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

A `ID` propriedade é a coluna de chave primária (**CP**) da tabela de banco de dados que corresponde a essa classe. Por padrão, o EF interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária. Por exemplo, a CP poderia ser nomeada `StudentID` em vez de `ID` .

A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships). As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade. A `Enrollments` propriedade de uma `Student` entidade:

* Contém todas as `Enrollment` entidades relacionadas a essa `Student` entidade.
* Se uma `Student` linha específica no banco de dados tiver duas `Enrollment` linhas relacionadas:
  * A `Student` propriedade de `Enrollments` navegação dessa entidade contém essas duas `Enrollment` entidades.
  
`Enrollment` as linhas contêm o valor de CP de um aluno na `StudentID` coluna de chave estrangeira (**FK**).

Se uma propriedade de navegação puder conter várias entidades:

 * O tipo deve ser uma lista, como `ICollection<T>` , `List<T>` ou `HashSet<T>` .
 * As entidades podem ser adicionadas, excluídas e atualizadas.

Relações de navegação muitos para muitos e um para muitos podem conter várias entidades. Quando `ICollection<T>` é usado, o EF cria uma `HashSet<T>` coleção por padrão.

### <a name="the-enrollment-entity"></a>A entidade Enrollment

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

Na pasta *modelos* , crie a `Enrollment` classe com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

A `EnrollmentID` propriedade é a CP. Essa entidade usa o `classnameID` padrão em vez de `ID` por si só. A `Student` entidade usou o `ID` padrão. Alguns desenvolvedores preferem usar um padrão em todo o modelo de dados. Neste tutorial, a variação ilustra que qualquer padrão pode ser usado. Um [tutorial posterior](inheritance.md) mostra como o uso `ID` do sem ClassName facilita a implementação da herança no modelo de dados.

A propriedade `Grade` é um `enum`. O `?` após a `Grade` declaração de tipo indica que a `Grade` propriedade é [anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types). Uma classificação que `null` é diferente de uma classificação zero. `null` significa que uma classificação não é conhecida ou ainda não foi atribuída.

A `StudentID` propriedade é uma chave estrangeira (FK) e a propriedade de navegação correspondente é `Student` . Uma `Enrollment` entidade está associada a uma `Student` entidade, portanto, a propriedade só pode conter uma única `Student` entidade. Isso é diferente da `Student.Enrollments` propriedade de navegação, que pode conter várias `Enrollment` entidades.

A `CourseID` propriedade é um FK e a propriedade de navegação correspondente é `Course` . Uma entidade `Enrollment` está associada a uma entidade `Course`.

Entity Framework interpretará uma propriedade como uma propriedade de FK se for nomeada nome da `<` propriedade de navegação `><` nome da propriedade de chave primária `>` . Por exemplo, `StudentID` para a `Student` propriedade de navegação, uma vez que a `Student` CP da entidade é `ID` . As propriedades FK também podem ser nomeadas  `<` como nome da propriedade de chave primária `>` . Por exemplo, `CourseID` como a `Course` CP da entidade é `CourseID` .

### <a name="the-course-entity"></a>A entidade Course

![Diagrama de entidade Curso](intro/_static/course-entity.png)

Na pasta *modelos* , crie a `Course` classe com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

A propriedade `Enrollments` é uma propriedade de navegação. Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.

O atributo [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) é explicado em um [tutorial posterior](complex-data-model.md). Esse atributo permite inserir a CP para o curso em vez de fazer com que o banco de dados a gere.

## <a name="create-the-database-context"></a>Criar o contexto de banco de dados

A classe principal que coordena a funcionalidade do EF para um determinado modelo de dados é a <xref:Microsoft.EntityFrameworkCore.DbContext> classe de contexto Database. Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`. A `DbContext` classe derivada especifica quais entidades estão incluídas no modelo de dados. Alguns comportamentos do EF podem ser personalizados. Neste projeto, a classe é chamada `SchoolContext`.

Na pasta do projeto, crie uma pasta chamada `Data` .

Na pasta *dados* , crie uma `SchoolContext` classe com o seguinte código:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

O código anterior cria uma `DbSet` propriedade para cada conjunto de entidades. Na terminologia do EF:

* Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.
* Uma entidade corresponde a uma linha da tabela.

As `DbSet<Enrollment>` `DbSet<Course>` instruções e poderiam ser omitidas e funcionarão da mesma. O EF os incluiria implicitamente porque:

* A `Student` entidade referencia a `Enrollment` entidade.
* A `Enrollment` entidade referencia a `Course` entidade.

Quando o banco de dados é criado, o EF cria tabelas que têm nomes iguais aos nomes de propriedade `DbSet`. Os nomes de propriedade das coleções normalmente são plural. Por exemplo, `Students` em vez de `Student` . Os desenvolvedores não concordam sobre se os nomes de tabela devem ser pluralizados ou não. Para esses tutoriais, o comportamento padrão é substituído pela especificação de nomes de tabela singulares no `DbContext` . Para fazer isso, adicione o código realçado a seguir após a última propriedade DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Registrar o `SchoolContext`

O ASP.NET Core inclui a [injeção de dependência](../../fundamentals/dependency-injection.md). Serviços, como o contexto de banco de dados do EF, são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços, como controladores MVC, são fornecidos a esses serviços por meio de parâmetros do construtor. O código do construtor do controlador que obtém uma instância de contexto é mostrado posteriormente neste tutorial.

Para registrar `SchoolContext` como um serviço, abra *Startup.cs* e adicione as linhas realçadas ao método `ConfigureServices`.

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto `DbContextOptionsBuilder`. Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.

Abra o *appsettings.json* arquivo e adicione uma cadeia de conexão, conforme mostrado na seguinte marcação:

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>Adicionar o filtro de exceção de banco de dados

Adicione <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> ao `ConfigureServices` conforme mostrado no código a seguir:

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=6)]

O `AddDatabaseDeveloperPageExceptionFilter` fornece informações de erro úteis no [ambiente de desenvolvimento](xref:fundamentals/environments).

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Inicializar o BD com os dados de teste

O EF cria um banco de dados vazio. Nesta seção, é adicionado um método que é chamado depois que o banco de dados é criado para preenchê-lo com dado de teste.

O `EnsureCreated` método é usado para criar automaticamente o banco de dados. Em um [tutorial posterior](migrations.md), você verá como lidar com alterações de modelo usando migrações do Code First para alterar o esquema de banco de dados em vez de descartar e recriar o banco de dados.

Na pasta *dados* , crie uma nova classe chamada `DbInitializer` com o seguinte código:

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

O código anterior verifica se o banco de dados existe:

* Se o banco de dados não for encontrado;
  * Ele é criado e carregado com dados de teste. Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.
* Se o banco de dados for encontrado, ele não executará nenhuma ação.

Atualize *Program.cs* com o seguinte código:

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

*Program.cs* faz o seguinte na inicialização do aplicativo:

* Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.
* Chame o método `DbInitializer.Initialize` .
* Descarte o contexto quando o `Initialize` método for concluído, conforme mostrado no código a seguir:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

Na primeira vez em que o aplicativo é executado, o banco de dados é criado e carregado com o teste Data. Sempre que o modelo de dados for alterado:

* Exclua o banco de dados.
* Atualize o método semente e inicie o novamente com um novo banco de dados.

 Nos tutoriais posteriores, o banco de dados é modificado quando o modelo de dado é alterado, sem excluí-lo e recriá-lo. Nenhum dado é perdido quando o modelo de dados é alterado.

## <a name="create-controller-and-views"></a>Criar um controlador e exibições

Use o mecanismo scaffolding no Visual Studio para adicionar um controlador MVC e exibições que usarão o EF para consultar e salvar dados.

A criação automática de métodos e exibições de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) é conhecida como scaffolding.

* Em **Gerenciador de soluções**, clique com o botão direito do mouse na `Controllers` pasta e selecione **Adicionar > novo item com Scaffold**.
* Na caixa de diálogo **Adicionar Scaffolding**:
  * Selecione **Controlador MVC com exibições, usando o Entity Framework**.
  * Clique em **Adicionar**. A caixa de diálogo **Adicionar controlador MVC com exibições, usando Entity Framework** é exibida: ![ Scaffold Student](intro/_static/scaffold-student2.png)
  * Em **classe de modelo**, selecione **aluno**.
  * Na **classe de contexto de dados**, selecione **SchoolContext**.
  * Aceite o **StudentsController** padrão como o nome.
  * Clique em **Adicionar**.

O mecanismo de scaffolding do Visual Studio cria um `StudentsController.cs` arquivo e um conjunto de exibições ( `*.cshtml` arquivos) que funcionam com o controlador.

Observe que o controlador usa `SchoolContext` como um parâmetro de construtor.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

A injeção de dependência do ASP.NET Core é responsável por passar uma instância de `SchoolContext` para o controlador. Você configurou isso na `Startup` classe.

O controlador contém um método de ação `Index`, que exibe todos os alunos no banco de dados. O método obtém uma lista de alunos do conjunto de entidades Students pela leitura da propriedade `Students` da instância de contexto de banco de dados:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Os elementos de programação assíncrona neste código são explicados posteriormente no tutorial.

A exibição *Views/Students/Index.cshtml* mostra esta lista em uma tabela:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.

Clique na guia Alunos para ver os dados de teste inserido pelo método `DbInitializer.Initialize`. Dependendo da largura da janela do navegador, você verá o link da guia `Students` na parte superior da página ou precisará clicar no ícone de navegação no canto superior direito para ver o link.

![Home page estreita da Contoso University](intro/_static/home-page-narrow.png)

![Página Índice de Alunos](intro/_static/students-index.png)

## <a name="view-the-database"></a>Exibir o banco de dados

Quando o aplicativo é iniciado, o `DbInitializer.Initialize` método chama `EnsureCreated` . O EF observou que não havia nenhum banco de dados:

* Então, ele criou um banco de dados.
* O `Initialize` código do método preencheu o banco de dados com os Data.

Use **pesquisador de objetos do SQL Server** (SSOX) para exibir o banco de dados no Visual Studio:

* Selecione **pesquisador de objetos do SQL Server** no menu **Exibir** no Visual Studio.
* Em SSOX, selecione **(LocalDB) \MSSQLLocalDB bancos de dados de >**.
* Selecione `ContosoUniversity1` , a entrada para o nome do banco de dados que está na cadeia de conexão no *appsettings.json* arquivo.
* Expanda o nó **tabelas** para ver as tabelas no banco de dados.

![Tabelas no SSOX](intro/_static/ssox-tables.png)

Clique com o botão direito do mouse na tabela **Student** e clique em **exibir dados** para ver os dados na tabela.

![Tabela Aluno no SSOX](intro/_static/ssox-student-table.png)

Os `*.mdf` `*.ldf` arquivos de banco de dados e estão na pasta *C:\Users \\ \<username>* .

Como `EnsureCreated` é chamado no método inicializador que é executado no início do aplicativo, você pode:

* Faça uma alteração na `Student` classe.
* Exclua o banco de dados.
* Pare e inicie o aplicativo. O banco de dados é recriado automaticamente para corresponder à alteração.

Por exemplo, se uma `EmailAddress` propriedade for adicionada à `Student` classe, uma nova `EmailAddress` coluna na tabela recriada. A exibição em classe não exibirá a nova `EmailAddress` propriedade.

## <a name="conventions"></a>Convenções

A quantidade de código escrita para que o EF crie um banco de dados completo é mínima devido ao uso das convenções que o EF usa:

* Os nomes de propriedades `DbSet` são usadas como nomes de tabela. Para entidades não referenciadas por uma propriedade `DbSet`, os nomes de classe de entidade são usados como nomes de tabela.
* Os nomes de propriedade de entidade são usados para nomes de coluna.
* Propriedades de entidade que são nomeadas `ID` ou `classnameID` são reconhecidas como propriedades de CP.
* Uma propriedade será interpretada como uma propriedade de FK se for nomeada nome da `<` propriedade de navegação `><` nome da propriedade CP `>` . Por exemplo, `StudentID` para a `Student` propriedade de navegação, uma vez que a `Student` CP da entidade é `ID` . As propriedades FK também podem ser nomeadas `<` como nome da propriedade de chave primária `>` . Por exemplo, `EnrollmentID` como a `Enrollment` CP da entidade é `EnrollmentID` .

O comportamento convencional pode ser substituído. Por exemplo, os nomes de tabela podem ser especificados explicitamente, conforme mostrado anteriormente neste tutorial. Os nomes de coluna e qualquer propriedade podem ser definidos como uma CP ou FK.

## <a name="asynchronous-code"></a>Código assíncrono

A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.

Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso. Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados. Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S. Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações. Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.

O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, ao passo que, em situações de alto tráfego, a melhoria de desempenho potencial é significativa.

No código a seguir, `async` , `Task<T>` , `await` e faça com que `ToListAsync` o código seja executado de forma assíncrona.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* A palavra-chave `async` instrui o compilador a gerar retornos de chamada para partes do corpo do método e a criar automaticamente o objeto `Task<IActionResult>` que é retornado.
* O tipo de retorno `Task<IActionResult>` representa um trabalho em andamento com um resultado do tipo `IActionResult`.
* A palavra-chave `await` faz com que o compilador divida o método em duas partes. A primeira parte termina com a operação que é iniciada de forma assíncrona. A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.
* `ToListAsync` é a versão assíncrona do método de extensão `ToList`.

Algumas coisas a serem consideradas ao escrever código assíncrono que usa o EF:

* Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona. Isso inclui, por exemplo, `ToListAsync`, `SingleOrDefaultAsync` e `SaveChangesAsync`. Isso não inclui, por exemplo, instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Um contexto do EF não é thread-safe: não tente realizar várias operações em paralelo. Quando você chamar qualquer método assíncrono do EF, sempre use a palavra-chave `await`.
* Para aproveitar os benefícios de desempenho do código assíncrono, certifique-se de que qualquer pacote de biblioteca usado também use Async se eles chamarem quaisquer métodos do EF que façam com que as consultas sejam enviadas ao banco de dados.

Para obter mais informações sobre a programação assíncrona no .NET, consulte [Visão geral da programação assíncrona](/dotnet/articles/standard/async).

## <a name="limit-entities-fetched"></a>Limitar entidades buscadas

Consulte [Considerações sobre desempenho](xref:data/ef-rp/intro) para obter informações sobre como limitar o número ou as entidades retornadas de uma consulta.

Vá para o próximo tutorial para aprender a executar operações CRUD (criar, ler, atualizar e excluir) básicas.

> [!div class="nextstepaction"]
> [Implementar a funcionalidade CRUD básica](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

O aplicativo Web de exemplo Contoso University demonstra como criar aplicativos Web em ASP.NET Core 2.2 MVC usando o EF (Entity Framework) Core 2.2 e o Visual Studio 2017 ou 2019.

Este tutorial não foi atualizado para o ASP.NET Core 3,1. Ele foi atualizado para o [ASP.NET Core 5,0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).

O aplicativo de exemplo é um site de uma Contoso University fictícia. Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor. Este é o primeiro de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University do zero.

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do .NET Core 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com as cargas de trabalho a seguir:
  * Carga de trabalho **ASP.NET e desenvolvimento para a Web**
  * Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**

## <a name="troubleshooting"></a>Solução de problemas

Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Para obter uma lista de erros comuns e como resolvê-los, consulte [a seção Solução de problemas do último tutorial da série](advanced.md#common-errors). Caso não encontre o que precisa na seção, poste uma pergunta no StackOverflow.com sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Esta é uma série de dez tutoriais, cada um se baseando no que é feito nos tutoriais anteriores. Considere a possibilidade de salvar uma cópia do projeto após a conclusão bem-sucedida de cada tutorial. Caso tenha problemas, comece novamente no tutorial anterior em vez de voltar ao início de toda a série.

## <a name="contoso-university-web-app"></a>Aplicativo Web Contoso University

O aplicativo que você criará nestes tutoriais é um site simples de uma universidade.

Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores. Estas são algumas das telas que você criará.

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Criar um aplicativo Web

* Abra o Visual Studio.

* No menu **Arquivo**, selecione **Novo > Projeto**.

* No painel esquerdo, selecione **Instalado > Visual C# > Web**.

* Selecione o modelo de projeto **Aplicativo Web ASP.NET Core**.

* Insira **ContosoUniversity** como o nome e clique em **OK**.

  ![Caixa de diálogo Novo Projeto](intro/_static/new-project2.png)

* Aguarde a exibição da caixa de diálogo **novo ASP.NET Core aplicativo Web** .

* Selecione **.NET Core**, **ASP.NET Core 2.2** e o modelo **Aplicativo Web (Model-View-Controller)**.

* Verifique se a **autenticação** está definida como **sem autenticação**.

* Selecione **OK**

  ![Caixa de diálogo Novo projeto ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Configurar o estilo do site

Algumas alterações simples configurarão o menu do site, o layout e a home page.

Abra *Views/Shared/_Layout.cshtml* e faça as seguintes alterações:

* Altere cada ocorrência de "ContosoUniversity" para "Contoso University". Há três ocorrências.

* Adicione entradas de menu **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Privacidade**.

As alterações são realçadas.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

Em *Views/Home/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu. Você verá a home page com guias para as páginas que você criará nestes tutoriais.

![Home page da Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Sobre pacotes NuGet do EF Core

Para adicionar o suporte do EF Core a um projeto, instale o provedor de banco de dados que você deseja ter como destino. Este tutorial usa o SQL Server e o pacote de provedor é [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Esse pacote está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), portanto, você não precisa referenciar o pacote.

O pacote SQL Server do EF e suas dependências (`Microsoft.EntityFrameworkCore` e `Microsoft.EntityFrameworkCore.Relational`) fornecem suporte de runtime para o EF. Você adicionará um pacote de ferramentas posteriormente, no tutorial [Migrações](migrations.md).

Para obter informações sobre outros provedores de banco de dados que estão disponíveis para o Entity Framework Core, consulte [Provedores de banco de dados](/ef/core/providers/).

## <a name="create-the-data-model"></a>Criar o modelo de dados

Em seguida, você criará as classes de entidade para o aplicativo Contoso University. Você começará com as três entidades a seguir.

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`, e uma relação um-para-muitos entre as entidades `Course` e `Enrollment`. Em outras palavras, um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.

Nas seções a seguir, você criará uma classe para cada uma dessas entidades.

### <a name="the-student-entity"></a>A entidade Student

![Diagrama da entidade Student](intro/_static/student-entity.png)

Na pasta *Models*, crie um arquivo de classe chamado *Student.cs* e substitua o código de modelo pelo código a seguir.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

A propriedade `ID` se tornará a coluna de chave primária da tabela de banco de dados que corresponde a essa classe. Por padrão, o Entity Framework interpreta uma propriedade chamada `ID` ou `classnameID` como a chave primária.

A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships). As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade. Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazenará todas as entidades `Enrollment` relacionadas a essa entidade `Student`. Em outras palavras, se uma `Student` linha no banco de dados tiver duas `Enrollment` linhas relacionadas (linhas que contêm o valor de chave primária do aluno em sua coluna de chave estrangeira StudentId), a `Student` propriedade de navegação dessa entidade `Enrollments` conterá essas duas `Enrollment` entidades.

Se uma propriedade de navegação pode armazenar várias entidades (como em relações muitos para muitos ou um-para-muitos), o tipo precisa ser uma lista na qual entradas podem ser adicionadas, excluídas e atualizadas, como `ICollection<T>`. Especifique `ICollection<T>` ou um tipo, como `List<T>` ou `HashSet<T>`. Se você especificar `ICollection<T>`, o EF criará uma coleção `HashSet<T>` por padrão.

### <a name="the-enrollment-entity"></a>A entidade Enrollment

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

Na pasta *Models*, crie *Enrollment.cs* e substitua o código existente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

A propriedade `EnrollmentID` será a chave primária; essa entidade usa o padrão `classnameID` em vez de `ID` por si só, como você viu na entidade `Student`. Normalmente, você escolhe um padrão e usa-o em todo o modelo de dados. Aqui, a variação ilustra que você pode usar qualquer um dos padrões. Em um [tutorial posterior](inheritance.md), você verá como usar uma ID sem nome de classe facilita a implementação da herança no modelo de dados.

A propriedade `Grade` é um `enum`. O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo. Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.

A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`. Uma entidade `Enrollment` é associada a uma entidade `Student`, de modo que a propriedade possa armazenar apenas uma única entidade `Student` (ao contrário da propriedade de navegação `Student.Enrollments` que você viu anteriormente, que pode armazenar várias entidades `Enrollment`).

A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`. Uma entidade `Enrollment` está associada a uma entidade `Course`.

O Entity Framework interpreta uma propriedade como uma propriedade de chave estrangeira se ela é nomeada `<navigation property name><primary key property name>` (por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`). As propriedades de chave estrangeira também podem ser nomeadas apenas `<primary key property name>` (por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`).

### <a name="the-course-entity"></a>A entidade Course

![Diagrama de entidade Curso](intro/_static/course-entity.png)

Na pasta *Models*, crie *Course.cs* e substitua o código existente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

A propriedade `Enrollments` é uma propriedade de navegação. Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.

Falaremos mais sobre o atributo `DatabaseGenerated` em um [tutorial posterior](complex-data-model.md) desta série. Basicamente, esse atributo permite que você insira a chave primária do curso, em vez de fazer com que ela seja gerada pelo banco de dados.

## <a name="create-the-database-context"></a>Criar o contexto de banco de dados

A classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados é a classe de contexto de banco de dados. Você cria essa classe derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`. No código, especifique quais entidades são incluídas no modelo de dados. Também personalize o comportamento específico do Entity Framework. Neste projeto, a classe é chamada `SchoolContext`.

Na pasta do projeto, crie uma pasta chamada *Dados*.

Na pasta *Dados*, crie um novo arquivo de classe chamado *SchoolContext.cs* e substitua o código de modelo pelo seguinte código:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Esse código cria uma propriedade `DbSet` para cada conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.

Você pode omitir as instruções `DbSet<Enrollment>` e `DbSet<Course>` e elas funcionarão da mesma maneira. O Entity Framework inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.

Quando o banco de dados é criado, o EF cria tabelas que têm nomes iguais aos nomes de propriedade `DbSet`. Em geral, os nomes de propriedade de coleções são plurais (Alunos em vez de Aluno), mas os desenvolvedores não concordam sobre se os nomes de tabela devem ser pluralizados ou não. Para esses tutoriais, você substituirá o comportamento padrão especificando nomes singulares de tabela no DbContext. Para fazer isso, adicione o código realçado a seguir após a última propriedade DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

Compile o projeto como uma verificação de erros do compilador.

## <a name="register-the-schoolcontext"></a>Registrar o SchoolContext

O ASP.NET Core implementa a [injeção de dependência](../../fundamentals/dependency-injection.md) por padrão. Serviços (como o contexto de banco de dados do EF) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como controladores MVC) recebem esses serviços por meio de parâmetros do construtor. Você verá o código de construtor do controlador que obtém uma instância de contexto mais adiante neste tutorial.

Para registrar `SchoolContext` como um serviço, abra *Startup.cs* e adicione as linhas realçadas ao método `ConfigureServices`.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto `DbContextOptionsBuilder`. Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.

Adicione instruções `using` aos namespaces `ContosoUniversity.Data` e `Microsoft.EntityFrameworkCore` e, em seguida, compile o projeto.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Abra o *appsettings.json* arquivo e adicione uma cadeia de conexão, conforme mostrado no exemplo a seguir.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

A cadeia de conexão especifica um banco de dados LocalDB do SQL Server. LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o LocalDB cria arquivos de banco de dados *.mdf* no diretório `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Inicializar o BD com os dados de teste

O Entity Framework criará um banco de dados vazio para você. Nesta seção, você escreve um método que é chamado depois que o banco de dados é criado para populá-lo com os dados de teste.

Aqui, você usará o método `EnsureCreated` para criar o banco de dados automaticamente. Em um [tutorial posterior](migrations.md), você verá como manipular as alterações do modelo usando as Migrações do Code First para alterar o esquema de banco de dados, em vez de remover e recriar o banco de dados.

Na pasta *Data*, crie um novo arquivo de classe chamado *DbInitializer.cs* e substitua o código de modelo pelo código a seguir, que faz com que um banco de dados seja criado, quando necessário, e carrega dados de teste no novo banco de dados.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

O código verifica se há alunos no banco de dados e, se não há, ele pressupõe que o banco de dados é novo e precisa ser propagado com os dados de teste. Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.

Em *Program.cs*, modifique o método `Main` para fazer o seguinte na inicialização do aplicativo:

* Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.
* Chame o método de semente passando a ele o contexto.
* Descarte o contexto quando o método de semente for concluído.

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

Na primeira vez em que você executar o aplicativo, o banco de dados será criado e propagado com dado de teste. Sempre que você alterar o modelo de dados:

 * Exclua o banco de dados.
 * Atualize o método semente e inicie o novamente com um novo banco de dados da mesma maneira.
 
Nos próximos tutoriais, você verá como modificar o banco de dados quando o modelo de dados for alterado, sem excluí-lo e recriá-lo.

## <a name="create-controller-and-views"></a>Criar um controlador e exibições

Nesta seção, o mecanismo scaffolding no Visual Studio é usado para adicionar um controlador MVC e modos de exibição que usarão o EF para consultar e salvar dados.

A criação automática de métodos de ação CRUD e exibições é conhecida como scaffolding. O scaffolding difere da geração de código, em que o código gerado por scaffolding é um ponto de partida que você pode modificar de acordo com seus requisitos, enquanto que normalmente o código gerado não é modificado. Quando precisar personalizar o código gerado, use classes parciais ou regenere o código quando as coisas mudarem.

* Clique com o botão direito do mouse na pasta **Controladores** no **Gerenciador de Soluções** e selecione **Adicionar > Novo Item Gerado por Scaffolding**.
* Na caixa de diálogo **Adicionar Scaffolding**:
  * Selecione **Controlador MVC com exibições, usando o Entity Framework**.
  * Clique em **Adicionar**. A caixa de diálogo **Adicionar controlador MVC com exibições, usando Entity Framework** é exibida: ![ Scaffold Student](intro/_static/scaffold-student2.png)
  * Na **classe Model**, selecione **Aluno**.
  * Na **Classe de contexto de dados** selecione **SchoolContext**.
  * Aceite o **StudentsController** padrão como o nome.
  * Clique em **Adicionar**.

O mecanismo de scaffolding do Visual Studio cria um arquivo *StudentsController.cs* e um conjunto de exibições (arquivos *. cshtml* ) que funcionam com o controlador.

Observe que o controlador usa `SchoolContext` como um parâmetro de construtor.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

A injeção de dependência do ASP.NET Core é responsável por passar uma instância de `SchoolContext` para o controlador. Que foi configurado no arquivo *Startup.cs* .

O controlador contém um método de ação `Index`, que exibe todos os alunos no banco de dados. O método obtém uma lista de alunos do conjunto de entidades Students pela leitura da propriedade `Students` da instância de contexto de banco de dados:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Você aprenderá sobre os elementos de programação assíncrona neste código posteriormente no tutorial.

A exibição *Views/Students/Index.cshtml* mostra esta lista em uma tabela:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.

Clique na guia Alunos para ver os dados de teste inserido pelo método `DbInitializer.Initialize`. Dependendo da largura da janela do navegador, você verá o link da guia `Students` na parte superior da página ou precisará clicar no ícone de navegação no canto superior direito para ver o link.

![Home page estreita da Contoso University](intro/_static/home-page-narrow.png)

![Página Índice de Alunos](intro/_static/students-index.png)

## <a name="view-the-database"></a>Exibir o banco de dados

Quando você iniciou o aplicativo, o método `DbInitializer.Initialize` chamou `EnsureCreated`. O EF observou que não havia nenhum banco de dados e, portanto, ele criou um; em seguida, o restante do código do método `Initialize` populou o banco de dados com os dados. Use o **SSOX** (Pesquisador de Objetos do SQL Server) para exibir o banco de dados no Visual Studio.

Feche o navegador.

Se a janela do SSOX ainda não estiver aberta, selecione-a no menu **Exibir** do Visual Studio.

Em SSOX, clique em **(LocalDB) \MSSQLLocalDB > bancos** de dados e, em seguida, clique na entrada do nome do banco de dados que está na cadeia de conexão no *appsettings.json* arquivo.

Expanda o nó **tabelas** para ver as tabelas no banco de dados.

![Tabelas no SSOX](intro/_static/ssox-tables.png)

Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas que foram inseridas na tabela.

![Tabela Aluno no SSOX](intro/_static/ssox-student-table.png)

Os arquivos de banco de dados *.mdf* e *.ldf* estão na pasta *C:\Users\\\<username>*.

Como você está chamando `EnsureCreated` no método inicializador executado na inicialização do aplicativo, agora você pode fazer uma alteração na classe `Student`, excluir o banco de dados, executar novamente o aplicativo e o banco de dados será recriado automaticamente para que ele corresponda à alteração. Por exemplo, se você adicionar uma propriedade `EmailAddress` à classe `Student`, verá uma nova coluna `EmailAddress` na tabela recriada.

## <a name="conventions"></a>Convenções

A quantidade de código feita para que o Entity Framework possa criar um banco de dados completo para você é mínima, devido ao uso de convenções ou de suposições feitas pelo Entity Framework.

* Os nomes de propriedades `DbSet` são usadas como nomes de tabela. Para entidades não referenciadas por uma propriedade `DbSet`, os nomes de classe de entidade são usados como nomes de tabela.
* Os nomes de propriedade de entidade são usados para nomes de coluna.
* As propriedades de entidade que são nomeadas ID ou classnameID são reconhecidas como propriedades de chave primária.
* Uma propriedade será interpretada como uma propriedade de chave estrangeira se ela for nomeada *\<navigation property name>\<primary key property name>* (por exemplo, `StudentID` para a `Student` propriedade de navegação, uma vez que a `Student` chave primária da entidade é `ID` ). As propriedades de chave estrangeira também podem ser nomeadas simplesmente *\<primary key property name>* (por exemplo, `EnrollmentID` uma vez que a `Enrollment` chave primária da entidade é `EnrollmentID` ).

O comportamento convencional pode ser substituído. Por exemplo, você pode especificar os nomes de tabela de forma explícita, conforme visto anteriormente neste tutorial. Além disso, você pode definir nomes de coluna e qualquer propriedade como a chave primária ou chave estrangeira, como você verá em um [tutorial posterior](complex-data-model.md) desta série.

## <a name="asynchronous-code"></a>Código assíncrono

A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.

Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso. Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados. Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S. Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações. Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.

O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, ao passo que, em situações de alto tráfego, a melhoria de desempenho potencial é significativa.

No código a seguir, a palavra-chave `async`, o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* A palavra-chave `async` instrui o compilador a gerar retornos de chamada para partes do corpo do método e a criar automaticamente o objeto `Task<IActionResult>` que é retornado.
* O tipo de retorno `Task<IActionResult>` representa um trabalho em andamento com um resultado do tipo `IActionResult`.
* A palavra-chave `await` faz com que o compilador divida o método em duas partes. A primeira parte termina com a operação que é iniciada de forma assíncrona. A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.
* `ToListAsync` é a versão assíncrona do método de extensão `ToList`.

Algumas coisas a serem consideradas quando você estiver escrevendo um código assíncrono que usa o Entity Framework:

* Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona. Isso inclui, por exemplo, `ToListAsync`, `SingleOrDefaultAsync` e `SaveChangesAsync`. Isso não inclui, por exemplo, instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Um contexto do EF não é thread-safe: não tente realizar várias operações em paralelo. Quando você chamar qualquer método assíncrono do EF, sempre use a palavra-chave `await`.
* Se desejar aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca que você está usando (por exemplo, para paginação) também usam o código assíncrono se eles chamam métodos do Entity Framework que fazem com que consultas sejam enviadas ao banco de dados.

Para obter mais informações sobre a programação assíncrona no .NET, consulte [Visão geral da programação assíncrona](/dotnet/articles/standard/async).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial para aprender a executar operações CRUD (criar, ler, atualizar e excluir) básicas.

> [!div class="nextstepaction"]
> [Implementar a funcionalidade CRUD básica](crud.md)

::: moniker-end
