---
title: Adicionar, baixar e excluir dados do usuário para Identity o em um projeto ASP.NET Core
author: rick-anderson
description: Saiba como adicionar dados de usuário personalizados ao Identity em um projeto ASP.NET Core. Excluir dados por GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a71395e82ed15dae753888a438471495208a14da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631843"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="c61f7-104">Adicionar, baixar e excluir dados de usuário personalizados para Identity em um projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c61f7-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="c61f7-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c61f7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c61f7-106">Este artigo mostra como:</span><span class="sxs-lookup"><span data-stu-id="c61f7-106">This article shows how to:</span></span>

* <span data-ttu-id="c61f7-107">Adicione dados de usuário personalizados a um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c61f7-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="c61f7-108">Marque o modelo de dados de usuário personalizado com o <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributo para que ele seja automaticamente disponibilizado para download e exclusão.</span><span class="sxs-lookup"><span data-stu-id="c61f7-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="c61f7-109">Tornar os dados capazes de ser baixados e excluídos ajuda a atender aos requisitos de [GDPR](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="c61f7-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="c61f7-110">O exemplo de projeto é criado a partir de um Razor aplicativo Web de páginas, mas as instruções são semelhantes para um aplicativo web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c61f7-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c61f7-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c61f7-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c61f7-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c61f7-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="c61f7-113">Criar um Razor aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="c61f7-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c61f7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c61f7-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c61f7-115">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="c61f7-116">Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="c61f7-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="c61f7-117">Selecione **ASP.NET Core aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c61f7-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="c61f7-118">Selecione **ASP.NET Core 3,0** na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="c61f7-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="c61f7-119">Selecionar **aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c61f7-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="c61f7-120">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="c61f7-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="c61f7-121">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="c61f7-122">Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="c61f7-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="c61f7-123">Selecione **ASP.NET Core aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c61f7-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="c61f7-124">Selecione **ASP.NET Core 2,2** na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="c61f7-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="c61f7-125">Selecionar **aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c61f7-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="c61f7-126">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="c61f7-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="c61f7-127">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="c61f7-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="c61f7-128">Executar o Identity scaffolder</span><span class="sxs-lookup"><span data-stu-id="c61f7-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c61f7-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c61f7-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c61f7-130">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar**  >  **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c61f7-131">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **Identity**  >  **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c61f7-132">Na caixa de diálogo \*\*Adicionar Identity \*\* , as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c61f7-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="c61f7-133">Selecione o arquivo de layout existente  *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c61f7-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="c61f7-134">Selecione os seguintes arquivos a serem substituídos:</span><span class="sxs-lookup"><span data-stu-id="c61f7-134">Select the following files to override:</span></span>
    * <span data-ttu-id="c61f7-135">**Conta/registro**</span><span class="sxs-lookup"><span data-stu-id="c61f7-135">**Account/Register**</span></span>
    * <span data-ttu-id="c61f7-136">**Conta/gerenciamento/índice**</span><span class="sxs-lookup"><span data-stu-id="c61f7-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="c61f7-137">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c61f7-138">Aceite o tipo (**WebApp1. Models. WebApp1Context** se o projeto for denominado **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="c61f7-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="c61f7-139">Selecione o **+** botão para criar uma nova **classe de usuário**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="c61f7-140">Aceite o tipo (**WebApp1User** se o projeto for nomeado **WebApp1**) > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="c61f7-141">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="c61f7-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c61f7-142">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="c61f7-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c61f7-143">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="c61f7-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c61f7-144">Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (. csproj).</span><span class="sxs-lookup"><span data-stu-id="c61f7-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="c61f7-145">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="c61f7-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="c61f7-146">Execute o seguinte comando para listar as Identity Opções de scaffolder:</span><span class="sxs-lookup"><span data-stu-id="c61f7-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="c61f7-147">Na pasta do projeto, execute o Identity scaffolder:</span><span class="sxs-lookup"><span data-stu-id="c61f7-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="c61f7-148">Siga as instruções em [Migrations, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="c61f7-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="c61f7-149">Crie uma migração e atualize o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c61f7-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="c61f7-150">Adicione `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c61f7-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="c61f7-151">Adicione `<partial name="_LoginPartial" />` ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="c61f7-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="c61f7-152">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c61f7-152">Test the app:</span></span>
  * <span data-ttu-id="c61f7-153">Registrar um usuário</span><span class="sxs-lookup"><span data-stu-id="c61f7-153">Register a user</span></span>
  * <span data-ttu-id="c61f7-154">Selecione o novo nome de usuário (ao lado do link de **logout** ).</span><span class="sxs-lookup"><span data-stu-id="c61f7-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="c61f7-155">Talvez seja necessário expandir a janela ou selecionar o ícone da barra de navegação para mostrar o nome de usuário e outros links.</span><span class="sxs-lookup"><span data-stu-id="c61f7-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="c61f7-156">Selecione a guia **dados pessoais** .</span><span class="sxs-lookup"><span data-stu-id="c61f7-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="c61f7-157">Selecione o botão **baixar** e examinou a *PersonalData.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="c61f7-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="c61f7-158">Teste o botão **excluir** , que exclui o usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="c61f7-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="c61f7-159">Adicionar dados de usuário personalizados ao Identity BD</span><span class="sxs-lookup"><span data-stu-id="c61f7-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="c61f7-160">Atualize a `IdentityUser` classe derivada com propriedades personalizadas.</span><span class="sxs-lookup"><span data-stu-id="c61f7-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="c61f7-161">Se você tiver nomeado o projeto WebApp1, o arquivo será chamado *áreas/ Identity /Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="c61f7-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="c61f7-162">Atualize o arquivo com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c61f7-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="c61f7-163">As propriedades com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:</span><span class="sxs-lookup"><span data-stu-id="c61f7-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="c61f7-164">Excluído quando a página *areas/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* Razor chama `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="c61f7-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="c61f7-165">Incluídos nos dados baixados pela página *áreas/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="c61f7-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="c61f7-166">Atualizar a página conta/gerenciar/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="c61f7-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="c61f7-167">Atualize o `InputModel` em *áreas/ Identity /pages/Account/Manage/index.cshtml.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="c61f7-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="c61f7-168">Atualize as *áreas/ Identity /pages/Account/Manage/index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="c61f7-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="c61f7-169">Atualize as *áreas/ Identity /pages/Account/Manage/index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="c61f7-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="c61f7-170">Atualizar a página Account/Register. cshtml</span><span class="sxs-lookup"><span data-stu-id="c61f7-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="c61f7-171">Atualize o `InputModel` em *áreas/ Identity /pages/Account/Register.cshtml.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="c61f7-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="c61f7-172">Atualize as *áreas/ Identity /pages/Account/Register.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="c61f7-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="c61f7-173">Atualize as *áreas/ Identity /pages/Account/Register.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="c61f7-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="c61f7-174">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="c61f7-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="c61f7-175">Adicionar uma migração para os dados de usuário personalizados</span><span class="sxs-lookup"><span data-stu-id="c61f7-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c61f7-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c61f7-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c61f7-177">No **console do Gerenciador de pacotes**do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c61f7-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c61f7-178">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="c61f7-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="c61f7-179">Testar criar, exibir, baixar, excluir dados de usuário personalizados</span><span class="sxs-lookup"><span data-stu-id="c61f7-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="c61f7-180">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c61f7-180">Test the app:</span></span>

* <span data-ttu-id="c61f7-181">Registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="c61f7-181">Register a new user.</span></span>
* <span data-ttu-id="c61f7-182">Exiba os dados de usuário personalizados na `/Identity/Account/Manage` página.</span><span class="sxs-lookup"><span data-stu-id="c61f7-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="c61f7-183">Baixe e exiba os dados pessoais dos usuários na `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="c61f7-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="c61f7-184">Adicionar declarações ao Identity usando IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="c61f7-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="c61f7-185">Esta seção não é uma extensão do tutorial anterior.</span><span class="sxs-lookup"><span data-stu-id="c61f7-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="c61f7-186">Para aplicar as etapas a seguir ao aplicativo criado usando o tutorial, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span><span class="sxs-lookup"><span data-stu-id="c61f7-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="c61f7-187">Declarações adicionais podem ser adicionadas ao ASP.NET Core Identity usando a `IUserClaimsPrincipalFactory<T>` interface.</span><span class="sxs-lookup"><span data-stu-id="c61f7-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="c61f7-188">Essa classe pode ser adicionada ao aplicativo no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="c61f7-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c61f7-189">Adicione a implementação personalizada da classe da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="c61f7-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="c61f7-190">O código de demonstração usa a `ApplicationUser` classe.</span><span class="sxs-lookup"><span data-stu-id="c61f7-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="c61f7-191">Essa classe adiciona uma `IsAdmin` propriedade que é usada para adicionar a declaração adicional.</span><span class="sxs-lookup"><span data-stu-id="c61f7-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="c61f7-192">O `AdditionalUserClaimsPrincipalFactory` implementa a `UserClaimsPrincipalFactory` interface.</span><span class="sxs-lookup"><span data-stu-id="c61f7-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="c61f7-193">Uma nova declaração de função é adicionada ao `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="c61f7-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="c61f7-194">A declaração adicional pode ser usada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c61f7-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="c61f7-195">Em uma Razor página, a `IAuthorizationService` instância pode ser usada para acessar o valor da declaração.</span><span class="sxs-lookup"><span data-stu-id="c61f7-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
