---
title: Parte 4, trabalhar com um banco de dados
author: rick-anderson
description: Parte 4 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 09/26/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e78b5b6dab7145413ae8612bfeb352f328ec86a
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360705"
---
# <a name="part-4-work-with-a-database-and-aspnet-core"></a>Parte 4, trabalhar com um banco de dados e ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).

O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

O sistema de [configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString` chave. Para o desenvolvimento local, a configuração Obtém a cadeia de conexão do *appsettings.json* arquivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

A cadeia de conexão gerada será semelhante à seguinte:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão para um servidor de banco de dados de teste ou de produção. Para obter mais informações, consulte [Configuration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:\Users\<user>\`.

<a name="ssox"></a>
1. No menu **Exibir** , abra **SSOX** (Pesquisador de Objetos do SQL Server).

   ![Menu de exibição](sql/_static/5/ssox.png)

1. Clique com o botão direito do mouse na `Movie` tabela e selecione **Exibir Designer** :

   ![Menus contextuais abertos na tabela Movie](sql/_static/5/design.png)

   ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

   Observe o ícone de chave ao lado de `ID`. Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.

1. Clique com o botão direito do mouse na `Movie` tabela e selecione **exibir dados** :

   ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

O site do [SQLite](https://www.sqlite.org/) afirma:

> O SQLite é um mecanismo de banco de dados SQL independente, de alta confiabilidade, inserido, completo e de domínio público. O SQLite é o mecanismo de banco de dados mais usado no mundo.

Há muitas ferramentas de terceiros que você pode baixar para gerenciar e exibir um banco de dados SQLite. A imagem abaixo é do [Navegador do DB para SQLite](https://sqlitebrowser.org/). Se você tiver uma ferramenta favorita do SQLite, deixe um comentário sobre o que mais gosta dela.

![Navegador de BD para SQLite mostrando banco de dados de filmes](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Para este tutorial, o recurso de *migrações* Entity Framework Core é usado sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna. Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha. Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite. Em vez disso, quando o esquema é alterado, o banco de dados é Descartado e recriado.
>
>A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. Uma recompilação de tabela envolve:
>
>* Criar uma nova tabela.
>* Copiar dados da tabela antiga para a nova tabela.
>* Remover a tabela antiga.
>* Renomear a nova tabela.
>
>Para obter mais informações, consulte os seguintes recursos:
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
> * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Propagar o banco de dados

Create uma nova classe denominada `SeedData` na pasta *modelos* com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Se houver filmes no banco de dados, o inicializador de semente retornará e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Substitua o conteúdo do *Program.cs* pelo seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

No código anterior, o `Main` método foi modificado para fazer o seguinte:

* Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.
* Chame o `seedData.Initialize` método, passando a ele a instância de contexto do banco de dados.
* Descarte o contexto quando o método de semente for concluído. A [instrução using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garante que o contexto seja Descartado.

A seguinte exceção ocorre quando `Update-Database` não foi executada:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Delete todos os registros no banco de dados. Usar os links de exclusão no navegador ou do [SSOX](xref:tutorials/razor-pages/new-field#ssox)

1. Force o aplicativo a ser inicializado chamando os métodos na `Startup` classe, para que o método semente seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Pare e reinicie o IIS com qualquer uma das seguintes abordagens:

   1. Clique com o botão direito do mouse no ícone da bandeja do sistema IIS Express na área de notificação e selecione **sair** ou **parar site** :

      ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu contextual](sql/_static/stopIIS.png)

   1. Se o aplicativo estiver sendo executado no modo de não depuração, pressione <kbd>F5</kbd> para executar no modo de depuração.
   1. Se o aplicativo estiver no modo de depuração, pare o depurador e pressione <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Delete todos os registros no banco de dados, portanto, o método de semente será executado. Interrompa e inicie o aplicativo para propagar o banco de dados.

---

O aplicativo mostra os dados propagados:

![Aplicativo de filme aberto no navegador mostrando dados de filmes](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizar as páginas](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).

O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

O sistema de [configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString` chave. Para o desenvolvimento local, a configuração Obtém a cadeia de conexão do *appsettings.json* arquivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

A cadeia de conexão gerada será semelhante à seguinte:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão para um servidor de banco de dados de teste ou de produção. Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:\Users\<user>\`.

<a name="ssox"></a>
* No menu **Exibir** , abra **SSOX** (Pesquisador de Objetos do SQL Server).

  ![Menu de exibição](sql/_static/ssox.png)

* Clique com o botão direito do mouse na `Movie` tabela e selecione **Exibir Designer** :

  ![Menus contextuais abertos na tabela Movie](sql/_static/design.png)

  ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

Observe o ícone de chave ao lado de `ID`. Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.

* Clique com o botão direito do mouse na `Movie` tabela e selecione **exibir dados** :

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

O site do [SQLite](https://www.sqlite.org/) afirma:

> O SQLite é um mecanismo de banco de dados SQL independente, de alta confiabilidade, inserido, completo e de domínio público. O SQLite é o mecanismo de banco de dados mais usado no mundo.

Há muitas ferramentas de terceiros que você pode baixar para gerenciar e exibir um banco de dados SQLite. A imagem abaixo é do [Navegador do DB para SQLite](https://sqlitebrowser.org/). Se você tiver uma ferramenta favorita do SQLite, deixe um comentário sobre o que mais gosta dela.

![Navegador de BD para SQLite mostrando banco de dados de filmes](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Para este tutorial, o recurso de *migrações* Entity Framework Core é usado sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna. Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha. Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite. Em vez disso, quando o esquema é alterado, o banco de dados é Descartado e recriado.
>
>A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. Uma recompilação de tabela envolve:
>
>* Criar uma nova tabela.
>* Copiar dados da tabela antiga para a nova tabela.
>* Remover a tabela antiga.
>* Renomear a nova tabela.
>
>Para obter mais informações, consulte os seguintes recursos:
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
> * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Propagar o banco de dados

Create uma nova classe denominada `SeedData` na pasta *modelos* com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Se houver filmes no banco de dados, o inicializador de semente retornará e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Substitua o conteúdo do *Program.cs* pelo seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

No código anterior, o `Main` método foi modificado para fazer o seguinte:

* Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.
* Chame o `seedData.Initialize` método, passando a ele a instância de contexto do banco de dados.
* Descarte o contexto quando o método de semente for concluído. A [instrução using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garante que o contexto seja Descartado.

A seguinte exceção ocorre quando `Update-Database` não foi executada:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Delete todos os registros no banco de dados. Use os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Force o aplicativo a ser inicializado chamando os métodos na `Startup` classe, para que o método semente seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Pare e reinicie o IIS com qualquer uma das seguintes abordagens:

  * Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site** :

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * Se o aplicativo estiver sendo executado no modo de não depuração, pressione <kbd>F5</kbd> para executar no modo de depuração.
    * Se o aplicativo estiver no modo de depuração, pare o depurador e pressione <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Delete todos os registros no banco de dados, portanto, o método de semente será executado. Interrompa e inicie o aplicativo para propagar o banco de dados.

---

O aplicativo mostra os dados propagados:

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55https.png)

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizar as páginas](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).

O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Para obter mais informações sobre os métodos usados em `ConfigureServices`, veja:

* [Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) da UE no ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

O sistema de [configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString` chave. Para o desenvolvimento local, a configuração Obtém a cadeia de conexão do *appsettings.json* arquivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

A cadeia de conexão gerada será semelhante à seguinte:

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão para um servidor de banco de dados de teste ou de produção. Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.

<a name="ssox"></a>
* No menu **Exibir** , abra **SSOX** (Pesquisador de Objetos do SQL Server).

  ![Menu de exibição](sql/_static/ssox.png)

* Clique com o botão direito do mouse na `Movie` tabela e selecione **Exibir Designer** :

  ![Menu contextual aberto na tabela Movie](sql/_static/design.png)

  ![Tabela Movie aberta no Designer](sql/_static/dv.png)

Observe o ícone de chave ao lado de `ID`. Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.

* Clique com o botão direito do mouse na `Movie` tabela e selecione **exibir dados** :

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Propagar o banco de dados

Create uma nova classe denominada `SeedData` na pasta *modelos* com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Se houver filmes no banco de dados, o inicializador de semente retornará e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Substitua o conteúdo do *Program.cs* pelo seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

No código anterior, o `Main` método foi modificado para fazer o seguinte:

* Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.
* Chame o `seedData.Initialize` método, passando a ele a instância de contexto do banco de dados.
* Descarte o contexto quando o método de semente for concluído. A [instrução using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garante que o contexto seja Descartado.

Um aplicativo de produção não chamaria `Database.Migrate`. Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:

SqlException: não é possível abrir Razor o banco de dados "PagesMovieContext-21" solicitado pelo logon. Falha no logon.
O logon falhou para o usuário 'user name'.

### <a name="test-the-app"></a>Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Delete todos os registros no banco de dados. Você pode fazer isso com os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Force o aplicativo a ser inicializado chamando os métodos na `Startup` classe, para que o método semente seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Faça isso com uma das seguintes abordagens:

  * Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site** :

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * Se o aplicativo estiver sendo executado no modo de não depuração, pressione <kbd>F5</kbd> para executar no modo de depuração.
    * Se o aplicativo estiver no modo de depuração, pare o depurador e pressione <kbd>F5</kbd>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Delete todos os registros no banco de dados, portanto, o método de semente será executado. Interrompa e inicie o aplicativo para propagar o banco de dados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Delete todos os registros no banco de dados, portanto, o método de semente será executado. Interrompa e inicie o aplicativo para propagar o banco de dados.

---

O aplicativo mostra os dados propagados:

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55https.png)

O próximo tutorial limpará a apresentação dos dados.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizar as páginas](xref:tutorials/razor-pages/da1)

::: moniker-end
