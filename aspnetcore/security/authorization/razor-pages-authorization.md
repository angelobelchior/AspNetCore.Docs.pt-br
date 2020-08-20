---
title: Razor Páginas convenções de autorização no ASP.NET Core
author: rick-anderson
description: Saiba como controlar o acesso a páginas com convenções que autorizam usuários e permitem que usuários anônimos acessem páginas ou pastas de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: b17717e7b3cbaa64d4ff00661f15439728f161d3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634885"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="60df3-103">Razor Páginas convenções de autorização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60df3-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="60df3-104">Uma maneira de controlar o acesso em seu Razor aplicativo de páginas é usar as convenções de autorização na inicialização.</span><span class="sxs-lookup"><span data-stu-id="60df3-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="60df3-105">Essas convenções permitem autorizar usuários e permitir que usuários anônimos acessem páginas ou pastas individuais de páginas.</span><span class="sxs-lookup"><span data-stu-id="60df3-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="60df3-106">As convenções descritas neste tópico aplicam automaticamente [filtros de autorização](xref:mvc/controllers/filters#authorization-filters) para controlar o acesso.</span><span class="sxs-lookup"><span data-stu-id="60df3-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="60df3-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60df3-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="60df3-108">O aplicativo de exemplo usa a [ cookie autenticação sem ASP.NET Core Identity ](xref:security/authentication/cookie)o.</span><span class="sxs-lookup"><span data-stu-id="60df3-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="60df3-109">Os conceitos e exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam o ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="60df3-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="60df3-110">Para usar ASP.NET Core Identity o, siga as orientações em <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="60df3-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="60df3-111">Exigir autorização para acessar uma página</span><span class="sxs-lookup"><span data-stu-id="60df3-111">Require authorization to access a page</span></span>

<span data-ttu-id="60df3-112">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convenção para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="60df3-113">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.</span><span class="sxs-lookup"><span data-stu-id="60df3-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="60df3-114">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="60df3-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="60df3-115">Um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pode ser aplicado a uma classe de modelo de página com o `[Authorize]` atributo Filter.</span><span class="sxs-lookup"><span data-stu-id="60df3-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="60df3-116">Para obter mais informações, consulte [autorizar atributo de filtro](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="60df3-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="60df3-117">Exigir autorização para acessar uma pasta de páginas</span><span class="sxs-lookup"><span data-stu-id="60df3-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="60df3-118">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convenção para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as páginas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="60df3-119">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.</span><span class="sxs-lookup"><span data-stu-id="60df3-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="60df3-120">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="60df3-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="60df3-121">Exigir autorização para acessar uma página de área</span><span class="sxs-lookup"><span data-stu-id="60df3-121">Require authorization to access an area page</span></span>

<span data-ttu-id="60df3-122">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convenção para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página de área no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="60df3-123">O nome da página é o caminho do arquivo sem uma extensão relativa ao diretório raiz de páginas para a área especificada.</span><span class="sxs-lookup"><span data-stu-id="60df3-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60df3-124">Por exemplo, o nome da página para o arquivo *areas/ Identity /pages/Manage/accounts.cshtml* é */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="60df3-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="60df3-125">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="60df3-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="60df3-126">Exigir autorização para acessar uma pasta de áreas</span><span class="sxs-lookup"><span data-stu-id="60df3-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="60df3-127">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convenção para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as áreas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="60df3-128">O caminho da pasta é o caminho da pasta em relação ao diretório raiz de páginas da área especificada.</span><span class="sxs-lookup"><span data-stu-id="60df3-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60df3-129">Por exemplo, o caminho da pasta para os arquivos em *áreas/ Identity /pages/Manage/* é */Manage*.</span><span class="sxs-lookup"><span data-stu-id="60df3-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="60df3-130">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="60df3-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="60df3-131">Permitir acesso anônimo a uma página</span><span class="sxs-lookup"><span data-stu-id="60df3-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="60df3-132">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convenção para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a uma página no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="60df3-133">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.</span><span class="sxs-lookup"><span data-stu-id="60df3-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="60df3-134">Permitir acesso anônimo a uma pasta de páginas</span><span class="sxs-lookup"><span data-stu-id="60df3-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="60df3-135">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convenção para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a todas as páginas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="60df3-136">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.</span><span class="sxs-lookup"><span data-stu-id="60df3-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="60df3-137">Observação sobre a combinação de acesso autorizado e anônimo</span><span class="sxs-lookup"><span data-stu-id="60df3-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="60df3-138">É válido especificar que uma pasta de páginas requer autorização e, em seguida, especificar que uma página dentro dessa pasta permite o acesso anônimo:</span><span class="sxs-lookup"><span data-stu-id="60df3-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="60df3-139">O inverso, no entanto, não é válido.</span><span class="sxs-lookup"><span data-stu-id="60df3-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="60df3-140">Você não pode declarar uma pasta de páginas para acesso anônimo e, em seguida, especificar uma página dentro dessa pasta que requer autorização:</span><span class="sxs-lookup"><span data-stu-id="60df3-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="60df3-141">A solicitação de autorização na página privada falhará.</span><span class="sxs-lookup"><span data-stu-id="60df3-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="60df3-142">Quando os <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> são aplicados à página, o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> tem precedência e controla o acesso.</span><span class="sxs-lookup"><span data-stu-id="60df3-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60df3-143">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60df3-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="60df3-144">Uma maneira de controlar o acesso em seu Razor aplicativo de páginas é usar as convenções de autorização na inicialização.</span><span class="sxs-lookup"><span data-stu-id="60df3-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="60df3-145">Essas convenções permitem autorizar usuários e permitir que usuários anônimos acessem páginas ou pastas individuais de páginas.</span><span class="sxs-lookup"><span data-stu-id="60df3-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="60df3-146">As convenções descritas neste tópico aplicam automaticamente [filtros de autorização](xref:mvc/controllers/filters#authorization-filters) para controlar o acesso.</span><span class="sxs-lookup"><span data-stu-id="60df3-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="60df3-147">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60df3-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="60df3-148">O aplicativo de exemplo usa a [ cookie autenticação sem ASP.NET Core Identity ](xref:security/authentication/cookie)o.</span><span class="sxs-lookup"><span data-stu-id="60df3-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="60df3-149">Os conceitos e exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam o ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="60df3-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="60df3-150">Para usar ASP.NET Core Identity o, siga as orientações em <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="60df3-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="60df3-151">Exigir autorização para acessar uma página</span><span class="sxs-lookup"><span data-stu-id="60df3-151">Require authorization to access a page</span></span>

<span data-ttu-id="60df3-152">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="60df3-153">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.</span><span class="sxs-lookup"><span data-stu-id="60df3-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="60df3-154">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="60df3-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="60df3-155">Um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pode ser aplicado a uma classe de modelo de página com o `[Authorize]` atributo Filter.</span><span class="sxs-lookup"><span data-stu-id="60df3-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="60df3-156">Para obter mais informações, consulte [autorizar atributo de filtro](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="60df3-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="60df3-157">Exigir autorização para acessar uma pasta de páginas</span><span class="sxs-lookup"><span data-stu-id="60df3-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="60df3-158">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as páginas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="60df3-159">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.</span><span class="sxs-lookup"><span data-stu-id="60df3-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="60df3-160">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="60df3-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="60df3-161">Exigir autorização para acessar uma página de área</span><span class="sxs-lookup"><span data-stu-id="60df3-161">Require authorization to access an area page</span></span>

<span data-ttu-id="60df3-162">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página de área no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="60df3-163">O nome da página é o caminho do arquivo sem uma extensão relativa ao diretório raiz de páginas para a área especificada.</span><span class="sxs-lookup"><span data-stu-id="60df3-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60df3-164">Por exemplo, o nome da página para o arquivo *areas/ Identity /pages/Manage/accounts.cshtml* é */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="60df3-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="60df3-165">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="60df3-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="60df3-166">Exigir autorização para acessar uma pasta de áreas</span><span class="sxs-lookup"><span data-stu-id="60df3-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="60df3-167">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as áreas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="60df3-168">O caminho da pasta é o caminho da pasta em relação ao diretório raiz de páginas da área especificada.</span><span class="sxs-lookup"><span data-stu-id="60df3-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60df3-169">Por exemplo, o caminho da pasta para os arquivos em *áreas/ Identity /pages/Manage/* é */Manage*.</span><span class="sxs-lookup"><span data-stu-id="60df3-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="60df3-170">Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="60df3-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="60df3-171">Permitir acesso anônimo a uma página</span><span class="sxs-lookup"><span data-stu-id="60df3-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="60df3-172">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a uma página no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="60df3-173">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.</span><span class="sxs-lookup"><span data-stu-id="60df3-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="60df3-174">Permitir acesso anônimo a uma pasta de páginas</span><span class="sxs-lookup"><span data-stu-id="60df3-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="60df3-175">Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a todas as páginas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="60df3-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="60df3-176">O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.</span><span class="sxs-lookup"><span data-stu-id="60df3-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="60df3-177">Observação sobre a combinação de acesso autorizado e anônimo</span><span class="sxs-lookup"><span data-stu-id="60df3-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="60df3-178">É válido especificar que uma pasta de páginas que requer autorização e o que especifica que uma página dentro dessa pasta permite o acesso anônimo:</span><span class="sxs-lookup"><span data-stu-id="60df3-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="60df3-179">O inverso, no entanto, não é válido.</span><span class="sxs-lookup"><span data-stu-id="60df3-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="60df3-180">Você não pode declarar uma pasta de páginas para acesso anônimo e, em seguida, especificar uma página dentro dessa pasta que requer autorização:</span><span class="sxs-lookup"><span data-stu-id="60df3-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="60df3-181">A solicitação de autorização na página privada falhará.</span><span class="sxs-lookup"><span data-stu-id="60df3-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="60df3-182">Quando os <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> são aplicados à página, o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> tem precedência e controla o acesso.</span><span class="sxs-lookup"><span data-stu-id="60df3-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60df3-183">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60df3-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
