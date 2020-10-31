---
title: 'Scaffold :::no-loc(Identity)::: em projetos de ASP.NET Core'
author: rick-anderson
description: 'Saiba como Scaffold :::no-loc(Identity)::: em um projeto ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141489"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="00521-103">Scaffold :::no-loc(Identity)::: em projetos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00521-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="00521-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="00521-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="00521-105">O ASP.NET Core fornece [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) como uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="00521-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="00521-106">Os aplicativos que incluem :::no-loc(Identity)::: o podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="00521-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="00521-107">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="00521-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="00521-108">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="00521-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="00521-109">O código gerado tem precedência sobre o mesmo código no :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="00521-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="00521-110">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar :::no-loc(Identity)::: origem da interface do usuário completa](#full).</span><span class="sxs-lookup"><span data-stu-id="00521-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="00521-111">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o :::no-loc(Identity)::: pacote RCL.</span><span class="sxs-lookup"><span data-stu-id="00521-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="00521-112">Você tem a opção de selecionar o :::no-loc(Identity)::: código a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="00521-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="00521-113">Embora o scaffolder gere a maior parte do código necessário, você precisa atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="00521-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="00521-114">Este documento explica as etapas necessárias para concluir uma :::no-loc(Identity)::: atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="00521-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="00521-115">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="00521-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="00521-116">Inspecione as alterações depois de executar o :::no-loc(Identity)::: scaffolder.</span><span class="sxs-lookup"><span data-stu-id="00521-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="00521-117">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com o :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="00521-118">Serviços ou stubs de serviço não são gerados quando scaffolding :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="00521-119">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="00521-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="00521-120">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="00521-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="00521-121">Quando scaffolding :::no-loc(Identity)::: com um novo contexto de dados em um projeto com contas individuais existentes:</span><span class="sxs-lookup"><span data-stu-id="00521-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="00521-122">No `Startup.ConfigureServices` , remova as chamadas para:</span><span class="sxs-lookup"><span data-stu-id="00521-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="00521-123">Por exemplo, `AddDbContext` e `AddDefault:::no-loc(Identity):::` são comentados no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="00521-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="00521-124">O código anterior comenta o código que está duplicado em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="00521-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="00521-125">Normalmente, os aplicativos que foram criados com contas individuais devem \* **não** criar um novo contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="00521-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="00521-126">Scaffold :::no-loc(Identity)::: em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="00521-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-127">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="00521-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="00521-128">Scaffold :::no-loc(Identity)::: em um :::no-loc(Razor)::: projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="00521-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-129">:::no-loc(Identity):::está configurado em _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="00521-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="00521-130">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="00521-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="00521-131">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="00521-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="00521-132">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="00521-132">Enable authentication</span></span>

<span data-ttu-id="00521-133">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="00521-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="00521-134">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="00521-134">Layout changes</span></span>

<span data-ttu-id="00521-135">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="00521-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="00521-136">Scaffold :::no-loc(Identity)::: em um :::no-loc(Razor)::: projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="00521-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="00521-137">Algumas :::no-loc(Identity)::: opções são configuradas em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-138">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="00521-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="00521-139">Scaffold :::no-loc(Identity)::: em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="00521-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-140">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="00521-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="00521-141">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="00521-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="00521-142">:::no-loc(Identity):::é configurado em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-143">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="00521-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="00521-144">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="00521-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="00521-145">Scaffold :::no-loc(Identity)::: em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="00521-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="00521-146">Scaffold :::no-loc(Identity)::: em um :::no-loc(Blazor Server)::: projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="00521-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-147">:::no-loc(Identity):::é configurado em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-148">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="00521-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="00521-149">Migrações</span><span class="sxs-lookup"><span data-stu-id="00521-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="00521-150">Passar um token XSRF para o aplicativo</span><span class="sxs-lookup"><span data-stu-id="00521-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="00521-151">Tokens podem ser passados para componentes:</span><span class="sxs-lookup"><span data-stu-id="00521-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="00521-152">Quando os tokens de autenticação são provisionados e salvos na autenticação :::no-loc(cookie)::: , eles podem ser passados para os componentes do.</span><span class="sxs-lookup"><span data-stu-id="00521-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="00521-153">:::no-loc(Razor)::: os componentes não podem usar `HttpContext` diretamente, portanto, não há como obter um [token de XSRF (falsificação de solicitação)](xref:security/anti-request-forgery) para postar no :::no-loc(Identity)::: ponto de extremidade de logout em `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="00521-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="00521-154">Um token XSRF pode ser passado para componentes.</span><span class="sxs-lookup"><span data-stu-id="00521-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="00521-155">Para obter mais informações, consulte <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="00521-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="00521-156">No arquivo *pages/_Host. cshtml* , estabeleça o token depois de adicioná-lo às `InitialApplicationState` `TokenProvider` classes e:</span><span class="sxs-lookup"><span data-stu-id="00521-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="00521-157">Atualize o `App` componente ( *app. Razor* ) para atribuir `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="00521-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="00521-158">O `TokenProvider` serviço demonstrado no tópico é usado no `LoginDisplay` componente na seção [layout e alterações de fluxo de autenticação](#layout-and-authentication-flow-changes) a seguir.</span><span class="sxs-lookup"><span data-stu-id="00521-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="00521-159">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="00521-159">Enable authentication</span></span>

<span data-ttu-id="00521-160">Na `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="00521-160">In the `Startup` class:</span></span>

* <span data-ttu-id="00521-161">Confirme se as :::no-loc(Razor)::: páginas de serviços são adicionadas no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="00521-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="00521-162">Se estiver usando o [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre o serviço.</span><span class="sxs-lookup"><span data-stu-id="00521-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="00521-163">Chame `UseDatabaseErrorPage` no Application Builder no `Startup.Configure` para o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="00521-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="00521-164">Chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="00521-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="00521-165">Adicione um ponto de extremidade para :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="00521-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="00521-166">Alterações no fluxo de layout e de autenticação</span><span class="sxs-lookup"><span data-stu-id="00521-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="00521-167">Adicione um `RedirectToLogin` componente ( *RedirectToLogin. Razor* ) à pasta *compartilhada* do aplicativo na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="00521-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Adicione um `LoginDisplay` componente ( *LoginDisplay. Razor* ) à pasta *compartilhada* do aplicativo. <span data-ttu-id="00521-169">O [serviço TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornece o token XSRF para o formulário HTML que posta para o :::no-loc(Identity)::: ponto de extremidade de logout:</span><span class="sxs-lookup"><span data-stu-id="00521-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="00521-170">No `MainLayout` componente ( *Shared/MainLayout. Razor* ), adicione o `LoginDisplay` componente ao `<div>` conteúdo do elemento de linha superior:</span><span class="sxs-lookup"><span data-stu-id="00521-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="00521-171">Pontos de extremidade de autenticação de estilo</span><span class="sxs-lookup"><span data-stu-id="00521-171">Style authentication endpoints</span></span>

<span data-ttu-id="00521-172">Como :::no-loc(Blazor Server)::: o usa páginas :::no-loc(Razor)::: :::no-loc(Identity)::: de páginas, o estilo da interface do usuário é alterado quando um visitante navega entre :::no-loc(Identity)::: páginas e componentes.</span><span class="sxs-lookup"><span data-stu-id="00521-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="00521-173">Você tem duas opções para abordar os estilos de incongruous:</span><span class="sxs-lookup"><span data-stu-id="00521-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="00521-174">Componentes de compilação :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="00521-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="00521-175">Uma abordagem para usar componentes do :::no-loc(Identity)::: em vez de páginas é criar :::no-loc(Identity)::: componentes.</span><span class="sxs-lookup"><span data-stu-id="00521-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="00521-176">Como `SignInManager` e `UserManager` não têm suporte em :::no-loc(Razor)::: componentes, use pontos de extremidade de API no :::no-loc(Blazor Server)::: aplicativo para processar ações de conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="00521-177">Usar um layout personalizado com :::no-loc(Blazor)::: estilos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="00521-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="00521-178">O :::no-loc(Identity)::: layout de páginas e os estilos podem ser modificados para produzir páginas que usam o :::no-loc(Blazor)::: tema padrão.</span><span class="sxs-lookup"><span data-stu-id="00521-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="00521-179">O exemplo nesta seção é meramente um ponto de partida para personalização.</span><span class="sxs-lookup"><span data-stu-id="00521-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="00521-180">Um trabalho adicional é provavelmente necessário para a melhor experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="00521-181">Crie um novo `NavMenu_:::no-loc(Identity):::Layout` componente ( *Shared/NavMenu_ :::no-loc(Identity)::: layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="00521-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="00521-182">Para a marcação e o código do componente, use o mesmo conteúdo do componente do aplicativo `NavMenu` ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="00521-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="00521-183">Remova todos os `NavLink` s para componentes que não podem ser acessados anonimamente porque os redirecionamentos automáticos no `RedirectToLogin` componente falham para componentes que exigem autenticação ou autorização.</span><span class="sxs-lookup"><span data-stu-id="00521-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="00521-184">No arquivo *pages/Shared/layout. cshtml* , faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="00521-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="00521-185">Adicione :::no-loc(Razor)::: diretivas à parte superior do arquivo para usar auxiliares de marca e os componentes do aplicativo na pasta *compartilhada* :</span><span class="sxs-lookup"><span data-stu-id="00521-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="00521-186">Substituir `{APPLICATION ASSEMBLY}` pelo nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="00521-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="00521-187">Adicione uma `<base>` marca e uma :::no-loc(Blazor)::: folha `<link>` de estilos ao `<head>` conteúdo:</span><span class="sxs-lookup"><span data-stu-id="00521-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="00521-188">Altere o conteúdo da `<body>` marca para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="00521-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="00521-189">Scaffold :::no-loc(Identity)::: em um :::no-loc(Blazor Server)::: projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="00521-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="00521-190">Algumas :::no-loc(Identity)::: opções são configuradas em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-191">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="00521-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="00521-192">Aplicativos autônomos ou hospedados :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="00521-192">Standalone or hosted :::no-loc(Blazor WebAssembly)::: apps</span></span>

<span data-ttu-id="00521-193">:::no-loc(Blazor WebAssembly):::Os aplicativos do lado do cliente usam suas próprias :::no-loc(Identity)::: abordagens de interface do usuário e não podem usar :::no-loc(ASP.NET Core Identity)::: scaffolding.</span><span class="sxs-lookup"><span data-stu-id="00521-193">Client-side :::no-loc(Blazor WebAssembly)::: apps use their own :::no-loc(Identity)::: UI approaches and can't use :::no-loc(ASP.NET Core Identity)::: scaffolding.</span></span> <span data-ttu-id="00521-194">Os aplicativos de ASP.NET Core do lado do servidor de soluções hospedadas :::no-loc(Blazor)::: podem seguir as :::no-loc(Razor)::: orientações das páginas/MVC deste artigo e são configurados como qualquer outro tipo de aplicativo ASP.NET Core que ofereça suporte ao :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-194">Server-side ASP.NET Core apps of hosted :::no-loc(Blazor)::: solutions can follow the :::no-loc(Razor)::: Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports :::no-loc(Identity):::.</span></span>

<span data-ttu-id="00521-195">A :::no-loc(Blazor)::: estrutura não inclui :::no-loc(Razor)::: versões de componentes de :::no-loc(Identity)::: páginas da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-195">The :::no-loc(Blazor)::: framework doesn't include :::no-loc(Razor)::: component versions of :::no-loc(Identity)::: UI pages.</span></span> <span data-ttu-id="00521-196">:::no-loc(Identity)::: Os componentes da interface do usuário :::no-loc(Razor)::: podem ser personalizados criados ou obtidos de fontes de terceiros sem suporte.</span><span class="sxs-lookup"><span data-stu-id="00521-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="00521-197">Para obter mais informações, consulte a [ :::no-loc(Blazor)::: segurança e os :::no-loc(Identity)::: artigos](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="00521-197">For more information, see the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="00521-198">Criar :::no-loc(Identity)::: origem da interface do usuário completa</span><span class="sxs-lookup"><span data-stu-id="00521-198">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="00521-199">Para manter o controle total da :::no-loc(Identity)::: interface do usuário, execute o :::no-loc(Identity)::: scaffolder e selecione **substituir todos os arquivos** .</span><span class="sxs-lookup"><span data-stu-id="00521-199">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="00521-200">O código realçado a seguir mostra as alterações para substituir a :::no-loc(Identity)::: interface do usuário padrão por :::no-loc(Identity)::: em um aplicativo web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="00521-200">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="00521-201">Talvez você queira fazer isso para ter controle total da interface do :::no-loc(Identity)::: usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-201">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="00521-202">O padrão :::no-loc(Identity)::: é substituído no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="00521-202">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="00521-203">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="00521-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="00521-204">Registre uma `IEmailSender` implementação, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="00521-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="00521-205">Configuração de senha</span><span class="sxs-lookup"><span data-stu-id="00521-205">Password configuration</span></span>

<span data-ttu-id="00521-206">Se <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em :::no-loc(Identity)::: páginas com Scaffold.</span><span class="sxs-lookup"><span data-stu-id="00521-206">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="00521-207">`InputModel``Password`as propriedades são encontradas nos seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="00521-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="00521-208">Desabilitar uma página</span><span class="sxs-lookup"><span data-stu-id="00521-208">Disable a page</span></span>

<span data-ttu-id="00521-209">Estas seções mostram como desabilitar a página de registro, mas a abordagem pode ser usada para desabilitar qualquer página.</span><span class="sxs-lookup"><span data-stu-id="00521-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="00521-210">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="00521-210">To disable user registration:</span></span>

* <span data-ttu-id="00521-211">Scaffold :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-211">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="00521-212">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="00521-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="00521-213">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="00521-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="00521-214">Atualizar *áreas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="00521-214">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="00521-215">Atualize *áreas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="00521-215">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="00521-216">Comente ou remova o link de registro de *áreas/ :::no-loc(Identity)::: /pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="00521-216">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="00521-217">Atualize a página *áreas/ :::no-loc(Identity)::: /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="00521-217">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="00521-218">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="00521-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="00521-219">Remova o código de confirmação do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="00521-219">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="00521-220">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="00521-220">Use another app to add users</span></span>

<span data-ttu-id="00521-221">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="00521-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="00521-222">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="00521-222">Options to add users include:</span></span>

* <span data-ttu-id="00521-223">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="00521-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="00521-224">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="00521-224">A console app.</span></span>

<span data-ttu-id="00521-225">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="00521-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="00521-226">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="00521-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="00521-227">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="00521-228">O usuário é adicionado ao :::no-loc(Identity)::: banco de dados.</span><span class="sxs-lookup"><span data-stu-id="00521-228">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="00521-229">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="00521-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="00521-230">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="00521-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="00521-231">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="00521-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="00521-232">Impedir a publicação de :::no-loc(Identity)::: ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="00521-232">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="00521-233">Para evitar a publicação de ativos estáticos na :::no-loc(Identity)::: raiz da Web, consulte <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="00521-233">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00521-234">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="00521-234">Additional resources</span></span>

* [<span data-ttu-id="00521-235">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="00521-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="00521-236">ASP.NET Core 2,1 e posterior fornece [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) como uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="00521-236">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="00521-237">Os aplicativos que incluem :::no-loc(Identity)::: o podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="00521-237">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="00521-238">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="00521-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="00521-239">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="00521-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="00521-240">O código gerado tem precedência sobre o mesmo código no :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="00521-240">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="00521-241">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).</span><span class="sxs-lookup"><span data-stu-id="00521-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="00521-242">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o :::no-loc(Identity)::: pacote RCL.</span><span class="sxs-lookup"><span data-stu-id="00521-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="00521-243">Você tem a opção de selecionar o :::no-loc(Identity)::: código a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="00521-243">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="00521-244">Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="00521-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="00521-245">Este documento explica as etapas necessárias para concluir uma :::no-loc(Identity)::: atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="00521-245">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="00521-246">Quando o :::no-loc(Identity)::: scaffolder é executado, um arquivo de *ScaffoldingReadme.txt* é criado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="00521-246">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="00521-247">O arquivo de *ScaffoldingReadme.txt* contém instruções gerais sobre o que é necessário para concluir a :::no-loc(Identity)::: atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="00521-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="00521-248">Este documento contém instruções mais completas do que o arquivo de *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="00521-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="00521-249">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="00521-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="00521-250">Inspecione as alterações depois de executar o :::no-loc(Identity)::: scaffolder.</span><span class="sxs-lookup"><span data-stu-id="00521-250">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="00521-251">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com o :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="00521-252">Serviços ou stubs de serviço não são gerados quando scaffolding :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-252">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="00521-253">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="00521-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="00521-254">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="00521-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="00521-255">Scaffold :::no-loc(Identity)::: em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="00521-255">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-256">Adicione as seguintes chamadas realçadas à `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="00521-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="00521-257">Scaffold :::no-loc(Identity)::: em um :::no-loc(Razor)::: projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="00521-257">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-258">:::no-loc(Identity):::é configurado em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-258">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-259">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="00521-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="00521-260">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="00521-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="00521-261">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="00521-261">Enable authentication</span></span>

<span data-ttu-id="00521-262">No `Configure` método da `Startup` classe, chame <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> após `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="00521-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="00521-263">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="00521-263">Layout changes</span></span>

<span data-ttu-id="00521-264">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="00521-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="00521-265">Scaffold :::no-loc(Identity)::: em um :::no-loc(Razor)::: projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="00521-265">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="00521-266">Algumas :::no-loc(Identity)::: opções são configuradas em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-266">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-267">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="00521-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="00521-268">Scaffold :::no-loc(Identity)::: em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="00521-268">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="00521-269">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="00521-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="00521-270">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="00521-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="00521-271">:::no-loc(Identity):::é configurado em *áreas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="00521-271">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="00521-272">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="00521-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="00521-273">Chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> após `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="00521-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="00521-274">Scaffold :::no-loc(Identity)::: em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="00521-274">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="00521-275">Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="00521-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="00521-276">Criar :::no-loc(Identity)::: origem da interface do usuário completa</span><span class="sxs-lookup"><span data-stu-id="00521-276">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="00521-277">Para manter o controle total da :::no-loc(Identity)::: interface do usuário, execute o :::no-loc(Identity)::: scaffolder e selecione **substituir todos os arquivos** .</span><span class="sxs-lookup"><span data-stu-id="00521-277">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="00521-278">O código realçado a seguir mostra as alterações para substituir a :::no-loc(Identity)::: interface do usuário padrão por :::no-loc(Identity)::: em um aplicativo web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="00521-278">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="00521-279">Talvez você queira fazer isso para ter controle total da interface do :::no-loc(Identity)::: usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-279">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="00521-280">O padrão :::no-loc(Identity)::: é substituído no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="00521-280">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="00521-281">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="00521-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="00521-282">Registre uma `IEmailSender` implementação, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="00521-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="00521-283">Configuração de senha</span><span class="sxs-lookup"><span data-stu-id="00521-283">Password configuration</span></span>

<span data-ttu-id="00521-284">Se <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em :::no-loc(Identity)::: páginas com Scaffold.</span><span class="sxs-lookup"><span data-stu-id="00521-284">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="00521-285">`InputModel``Password`as propriedades são encontradas nos seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="00521-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="00521-286">Página desabilitar registro</span><span class="sxs-lookup"><span data-stu-id="00521-286">Disable register page</span></span>

<span data-ttu-id="00521-287">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="00521-287">To disable user registration:</span></span>

* <span data-ttu-id="00521-288">Scaffold :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="00521-288">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="00521-289">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="00521-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="00521-290">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="00521-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="00521-291">Atualizar *áreas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="00521-291">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="00521-292">Atualize *áreas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="00521-292">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="00521-293">Comente ou remova o link de registro de *áreas/ :::no-loc(Identity)::: /pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="00521-293">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="00521-294">Atualize a página *áreas/ :::no-loc(Identity)::: /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="00521-294">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="00521-295">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="00521-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="00521-296">Remova o código de confirmação do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="00521-296">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="00521-297">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="00521-297">Use another app to add users</span></span>

<span data-ttu-id="00521-298">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="00521-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="00521-299">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="00521-299">Options to add users include:</span></span>

* <span data-ttu-id="00521-300">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="00521-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="00521-301">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="00521-301">A console app.</span></span>

<span data-ttu-id="00521-302">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="00521-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="00521-303">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="00521-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="00521-304">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="00521-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="00521-305">O usuário é adicionado ao :::no-loc(Identity)::: banco de dados.</span><span class="sxs-lookup"><span data-stu-id="00521-305">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="00521-306">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="00521-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="00521-307">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="00521-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="00521-308">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="00521-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00521-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="00521-309">Additional resources</span></span>

* [<span data-ttu-id="00521-310">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="00521-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
