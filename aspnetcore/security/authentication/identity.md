---
title: Introdução ao Identity no ASP.NET Core
author: rick-anderson
description: Use Identity com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160302"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="cdbb8-104">Introdução ao Identity no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdbb8-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cdbb8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cdbb8-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="cdbb8-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cdbb8-107">É uma API que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="cdbb8-108">Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="cdbb8-109">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cdbb8-110">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="cdbb8-111">O [ Identity código-fonte](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="cdbb8-112">[Scaffold Identity ](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para revisar a interação do modelo com o Identity .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="cdbb8-113">IdentityNormalmente, o é configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cdbb8-114">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cdbb8-115">Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cdbb8-116">Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="cdbb8-117">Para obter instruções mais detalhadas sobre como criar aplicativos que usam o Identity , consulte [próximas etapas](#next).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="cdbb8-118">A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="cdbb8-119">Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="cdbb8-120">Não relacionado a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="cdbb8-121">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cdbb8-122">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="cdbb8-122">Create a Web app with authentication</span></span>

<span data-ttu-id="cdbb8-123">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdbb8-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdbb8-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cdbb8-125">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cdbb8-126">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cdbb8-127">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cdbb8-128">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-128">Click **OK**.</span></span>
* <span data-ttu-id="cdbb8-129">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cdbb8-130">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdbb8-131">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="cdbb8-132">O comando anterior cria um Razor aplicativo Web usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="cdbb8-133">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="cdbb8-134">O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cdbb8-135">A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cdbb8-136">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-136">For example:</span></span>

* <span data-ttu-id="cdbb8-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cdbb8-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="cdbb8-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cdbb8-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cdbb8-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cdbb8-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cdbb8-140">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="cdbb8-140">Apply migrations</span></span>

<span data-ttu-id="cdbb8-141">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdbb8-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdbb8-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdbb8-143">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="cdbb8-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="cdbb8-144">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cdbb8-145">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="cdbb8-146">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cdbb8-147">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="cdbb8-147">Test Register and Login</span></span>

<span data-ttu-id="cdbb8-148">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-148">Run the app and register a user.</span></span> <span data-ttu-id="cdbb8-149">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="cdbb8-150">Configurar Identity serviços</span><span class="sxs-lookup"><span data-stu-id="cdbb8-150">Configure Identity services</span></span>

<span data-ttu-id="cdbb8-151">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cdbb8-152">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="cdbb8-153">O código realçado anterior Identity é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="cdbb8-154">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cdbb8-155">Identityé habilitado chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="cdbb8-156">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="cdbb8-157">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="cdbb8-158">`app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="cdbb8-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="cdbb8-160">Para obter mais informações sobre o `IdentityOptions` e o `Startup` , consulte <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="cdbb8-161">Scaffold registro, logon, LogOut e RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="cdbb8-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdbb8-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdbb8-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdbb8-163">Adicione os `Register` `Login` arquivos,, `LogOut` e `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="cdbb8-164">Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdbb8-165">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cdbb8-166">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cdbb8-167">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="cdbb8-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="cdbb8-168">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cdbb8-169">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="cdbb8-170">Para obter mais informações sobre scaffolding Identity , consulte [identidade do Scaffold em um Razor projeto com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cdbb8-171">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="cdbb8-171">Examine Register</span></span>

<span data-ttu-id="cdbb8-172">Quando um usuário clica no botão **registrar** na `Register` página, a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cdbb8-173">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="cdbb8-174">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="cdbb8-174">Log in</span></span>

<span data-ttu-id="cdbb8-175">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="cdbb8-176">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cdbb8-177">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cdbb8-178">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cdbb8-179">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cdbb8-180">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cdbb8-181">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="cdbb8-181">Log out</span></span>

<span data-ttu-id="cdbb8-182">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="cdbb8-183">No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="cdbb8-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cdbb8-185">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="cdbb8-186">TestarIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-186">Test Identity</span></span>

<span data-ttu-id="cdbb8-187">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cdbb8-188">Para testar Identity , adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="cdbb8-188">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cdbb8-189">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cdbb8-190">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="cdbb8-191">ApresentaIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-191">Explore Identity</span></span>

<span data-ttu-id="cdbb8-192">Para explorar Identity mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-192">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cdbb8-193">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="cdbb8-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cdbb8-194">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="cdbb8-195">IdentityQC</span><span class="sxs-lookup"><span data-stu-id="cdbb8-195">Identity Components</span></span>

<span data-ttu-id="cdbb8-196">Todos os Identity pacotes NuGet dependentes são incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-196">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="cdbb8-197">O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-197">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cdbb8-198">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-198">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="cdbb8-199">Migrando para o ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-199">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cdbb8-200">Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-200">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cdbb8-201">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="cdbb8-201">Setting password strength</span></span>

<span data-ttu-id="cdbb8-202">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="cdbb8-203">ADDDEFAULT Identity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-203">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cdbb8-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cdbb8-205">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-205">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cdbb8-206">Consulte a [ Identity fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-206">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="cdbb8-207">Impedir a publicação de Identity ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="cdbb8-207">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="cdbb8-208">Para evitar a publicação de ativos estáticos Identity (folhas de estilo e arquivos JavaScript para Identity a interface do usuário) na raiz da Web, adicione a seguinte `ResolveStaticWebAssetsInputsDependsOn` propriedade e `RemoveIdentityAssets` destino ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-208">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="cdbb8-209">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="cdbb8-209">Next Steps</span></span>

* <span data-ttu-id="cdbb8-210">[ASP.NET Core Identity código-fonte](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="cdbb8-210">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="cdbb8-211">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="cdbb8-212">ConfigurarIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cdbb8-213">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cdbb8-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cdbb8-214">ASP.NET Core Identity é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="cdbb8-215">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cdbb8-216">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cdbb8-217">Identityo pode ser configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cdbb8-218">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cdbb8-219">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cdbb8-220">Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cdbb8-221">Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="cdbb8-222">ADDDEFAULT Identity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cdbb8-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cdbb8-224">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cdbb8-225">Consulte a [ Identity fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-225">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cdbb8-226">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="cdbb8-226">Create a Web app with authentication</span></span>

<span data-ttu-id="cdbb8-227">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdbb8-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdbb8-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cdbb8-229">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cdbb8-230">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cdbb8-231">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cdbb8-232">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-232">Click **OK**.</span></span>
* <span data-ttu-id="cdbb8-233">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cdbb8-234">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdbb8-235">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="cdbb8-236">O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cdbb8-237">A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cdbb8-238">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-238">For example:</span></span>

* <span data-ttu-id="cdbb8-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cdbb8-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="cdbb8-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cdbb8-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cdbb8-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cdbb8-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cdbb8-242">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="cdbb8-242">Apply migrations</span></span>

<span data-ttu-id="cdbb8-243">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdbb8-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdbb8-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdbb8-245">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="cdbb8-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cdbb8-246">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cdbb8-247">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="cdbb8-247">Test Register and Login</span></span>

<span data-ttu-id="cdbb8-248">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-248">Run the app and register a user.</span></span> <span data-ttu-id="cdbb8-249">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="cdbb8-250">Configurar Identity serviços</span><span class="sxs-lookup"><span data-stu-id="cdbb8-250">Configure Identity services</span></span>

<span data-ttu-id="cdbb8-251">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cdbb8-252">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="cdbb8-253">O código anterior Identity é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="cdbb8-254">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cdbb8-255">Identityé habilitado chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="cdbb8-256">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="cdbb8-257">Para obter mais informações, consulte a [ Identity classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cdbb8-258">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="cdbb8-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="cdbb8-259">Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdbb8-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdbb8-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdbb8-261">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdbb8-262">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbb8-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cdbb8-263">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cdbb8-264">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="cdbb8-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cdbb8-265">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cdbb8-266">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cdbb8-267">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="cdbb8-267">Examine Register</span></span>

<span data-ttu-id="cdbb8-268">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cdbb8-269">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="cdbb8-270">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cdbb8-271">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cdbb8-272">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="cdbb8-272">Log in</span></span>

<span data-ttu-id="cdbb8-273">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="cdbb8-274">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cdbb8-275">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cdbb8-276">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cdbb8-277">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cdbb8-278">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cdbb8-279">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="cdbb8-279">Log out</span></span>

<span data-ttu-id="cdbb8-280">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="cdbb8-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cdbb8-282">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="cdbb8-283">TestarIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-283">Test Identity</span></span>

<span data-ttu-id="cdbb8-284">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cdbb8-285">Para testar Identity , adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-285">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cdbb8-286">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cdbb8-287">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="cdbb8-288">ApresentaIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-288">Explore Identity</span></span>

<span data-ttu-id="cdbb8-289">Para explorar Identity mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="cdbb8-289">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cdbb8-290">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="cdbb8-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cdbb8-291">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="cdbb8-292">IdentityQC</span><span class="sxs-lookup"><span data-stu-id="cdbb8-292">Identity Components</span></span>

<span data-ttu-id="cdbb8-293">Todos os Identity pacotes NuGet dependentes estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-293">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cdbb8-294">O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-294">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cdbb8-295">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="cdbb8-295">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="cdbb8-296">Migrando para o ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-296">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cdbb8-297">Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cdbb8-297">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cdbb8-298">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="cdbb8-298">Setting password strength</span></span>

<span data-ttu-id="cdbb8-299">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cdbb8-300">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="cdbb8-300">Next Steps</span></span>

* <span data-ttu-id="cdbb8-301">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdbb8-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="cdbb8-302">ConfigurarIdentity</span><span class="sxs-lookup"><span data-stu-id="cdbb8-302">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
