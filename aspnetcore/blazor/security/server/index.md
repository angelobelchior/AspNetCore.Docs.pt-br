---
title: 'Proteger ASP.NET Core Blazor Server aplicativos'
author: guardrex
description: 'Saiba como proteger aplicativos Blazor Server como ASP.NET Core aplicativos.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055471"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="aedf5-103">Proteger ASP.NET Core Blazor Server aplicativos</span><span class="sxs-lookup"><span data-stu-id="aedf5-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="aedf5-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aedf5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aedf5-105">Blazor Server os aplicativos são configurados para segurança da mesma maneira que ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="aedf5-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="aedf5-106">Para obter mais informações, consulte os artigos em <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="aedf5-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="aedf5-107">Os tópicos desta visão geral se aplicam especificamente ao Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="aedf5-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="aedf5-108">Blazor Server modelo de projeto</span><span class="sxs-lookup"><span data-stu-id="aedf5-108">Blazor Server project template</span></span>

<span data-ttu-id="aedf5-109">O Blazor Server modelo de projeto pode ser configurado para autenticação quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="aedf5-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aedf5-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aedf5-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aedf5-111">Siga as diretrizes do Visual Studio no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="aedf5-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="aedf5-112">Depois de escolher o modelo de **Blazor Server aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="aedf5-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="aedf5-113">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="aedf5-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="aedf5-114">**Sem autenticação**</span><span class="sxs-lookup"><span data-stu-id="aedf5-114">**No Authentication**</span></span>
* <span data-ttu-id="aedf5-115">**Contas de usuário individuais** : as contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="aedf5-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="aedf5-116">Dentro do aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="aedf5-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="aedf5-117">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="aedf5-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="aedf5-118">**Contas corporativas ou de estudante**</span><span class="sxs-lookup"><span data-stu-id="aedf5-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="aedf5-119">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="aedf5-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aedf5-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aedf5-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aedf5-121">Siga as diretrizes de Visual Studio Code no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="aedf5-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="aedf5-122">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="aedf5-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="aedf5-123">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="aedf5-123">Authentication mechanism</span></span> | <span data-ttu-id="aedf5-124">Descrição</span><span class="sxs-lookup"><span data-stu-id="aedf5-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="aedf5-125">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="aedf5-125">`None` (default)</span></span>         | <span data-ttu-id="aedf5-126">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="aedf5-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="aedf5-127">Usuários armazenados no aplicativo com ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="aedf5-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="aedf5-128">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="aedf5-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="aedf5-129">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="aedf5-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="aedf5-130">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="aedf5-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="aedf5-131">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="aedf5-131">Windows Authentication</span></span> |

<span data-ttu-id="aedf5-132">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="aedf5-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="aedf5-133">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="aedf5-133">Create a folder for the project.</span></span>
* <span data-ttu-id="aedf5-134">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="aedf5-134">Name the project.</span></span>

<span data-ttu-id="aedf5-135">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aedf5-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aedf5-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="aedf5-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="aedf5-137">Siga as diretrizes de Visual Studio para Mac no <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="aedf5-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="aedf5-138">Na etapa **configurar seu novo Blazor Server aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="aedf5-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="aedf5-139">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="aedf5-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aedf5-140">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="aedf5-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="aedf5-141">Crie um novo Blazor Server projeto com um mecanismo de autenticação usando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="aedf5-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="aedf5-142">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="aedf5-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="aedf5-143">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="aedf5-143">Authentication mechanism</span></span> | <span data-ttu-id="aedf5-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="aedf5-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="aedf5-145">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="aedf5-145">`None` (default)</span></span>         | <span data-ttu-id="aedf5-146">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="aedf5-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="aedf5-147">Usuários armazenados no aplicativo com ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="aedf5-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="aedf5-148">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="aedf5-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="aedf5-149">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="aedf5-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="aedf5-150">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="aedf5-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="aedf5-151">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="aedf5-151">Windows Authentication</span></span> |

<span data-ttu-id="aedf5-152">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="aedf5-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="aedf5-153">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="aedf5-153">Create a folder for the project.</span></span>
* <span data-ttu-id="aedf5-154">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="aedf5-154">Name the project.</span></span>

<span data-ttu-id="aedf5-155">Para mais informações:</span><span class="sxs-lookup"><span data-stu-id="aedf5-155">For more information:</span></span>

* <span data-ttu-id="aedf5-156">Consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aedf5-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="aedf5-157">Execute o comando de ajuda para o Blazor Server modelo ( `blazorserver` ) em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="aedf5-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="aedf5-158">Scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="aedf5-158">Scaffold Identity</span></span>

<span data-ttu-id="aedf5-159">Scaffold Identity em um Blazor Server projeto:</span><span class="sxs-lookup"><span data-stu-id="aedf5-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="aedf5-160">[Sem autorização existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="aedf5-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="aedf5-161">[Com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="aedf5-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aedf5-162">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="aedf5-162">Additional resources</span></span>

* [<span data-ttu-id="aedf5-163">Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aedf5-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="aedf5-164">Início Rápido: Proteger uma API Web ASP.NET Core com a plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="aedf5-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
