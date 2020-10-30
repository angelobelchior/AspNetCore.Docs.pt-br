---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053521"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="a9464-103">Áreas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9464-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="a9464-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9464-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9464-105">As áreas são um recurso ASP.NET usado para organizar a funcionalidade relacionada em um grupo como separado:</span><span class="sxs-lookup"><span data-stu-id="a9464-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="a9464-106">Namespace para roteamento.</span><span class="sxs-lookup"><span data-stu-id="a9464-106">Namespace for routing.</span></span>
* <span data-ttu-id="a9464-107">Estrutura de pastas para exibições e :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="a9464-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="a9464-108">O uso de áreas cria uma hierarquia para fins de roteamento adicionando outro parâmetro de rota, `area` , para `controller` e `action` ou uma :::no-loc(Razor)::: página `page` .</span><span class="sxs-lookup"><span data-stu-id="a9464-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="a9464-109">As áreas fornecem uma maneira de particionar um aplicativo Web ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de :::no-loc(Razor)::: páginas, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="a9464-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="a9464-110">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="a9464-111">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="a9464-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="a9464-112">O runtime do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="a9464-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="a9464-113">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="a9464-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="a9464-114">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="a9464-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="a9464-115">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, :::no-loc(Razor)::: páginas e modelos.</span><span class="sxs-lookup"><span data-stu-id="a9464-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="a9464-116">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="a9464-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="a9464-117">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="a9464-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="a9464-118">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="a9464-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="a9464-119">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a9464-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="a9464-120">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="a9464-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="a9464-121">Se você estiver usando :::no-loc(Razor)::: páginas, consulte [áreas com :::no-loc(Razor)::: páginas](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="a9464-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="a9464-122">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="a9464-122">Areas for controllers with views</span></span>

<span data-ttu-id="a9464-123">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a9464-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="a9464-124">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="a9464-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="a9464-125">Controladores com o [`[Area]`](#attribute) atributo para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="a9464-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="a9464-126">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="a9464-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="a9464-127">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="a9464-127">Area folder structure</span></span>

<span data-ttu-id="a9464-128">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços* .</span><span class="sxs-lookup"><span data-stu-id="a9464-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="a9464-129">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="a9464-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="a9464-130">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="a9464-130">Project name</span></span>
  * <span data-ttu-id="a9464-131">Áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-131">Areas</span></span>
    * <span data-ttu-id="a9464-132">Produtos</span><span class="sxs-lookup"><span data-stu-id="a9464-132">Products</span></span>
      * <span data-ttu-id="a9464-133">Controladores</span><span class="sxs-lookup"><span data-stu-id="a9464-133">Controllers</span></span>
        * <span data-ttu-id="a9464-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="a9464-134">HomeController.cs</span></span>
        * <span data-ttu-id="a9464-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="a9464-135">ManageController.cs</span></span>
      * <span data-ttu-id="a9464-136">Exibições</span><span class="sxs-lookup"><span data-stu-id="a9464-136">Views</span></span>
        * <span data-ttu-id="a9464-137">Página Inicial</span><span class="sxs-lookup"><span data-stu-id="a9464-137">Home</span></span>
          * <span data-ttu-id="a9464-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-138">Index.cshtml</span></span>
        * <span data-ttu-id="a9464-139">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="a9464-139">Manage</span></span>
          * <span data-ttu-id="a9464-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-140">Index.cshtml</span></span>
          * <span data-ttu-id="a9464-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-141">About.cshtml</span></span>
    * <span data-ttu-id="a9464-142">Serviços</span><span class="sxs-lookup"><span data-stu-id="a9464-142">Services</span></span>
      * <span data-ttu-id="a9464-143">Controladores</span><span class="sxs-lookup"><span data-stu-id="a9464-143">Controllers</span></span>
        * <span data-ttu-id="a9464-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="a9464-144">HomeController.cs</span></span>
      * <span data-ttu-id="a9464-145">Exibições</span><span class="sxs-lookup"><span data-stu-id="a9464-145">Views</span></span>
        * <span data-ttu-id="a9464-146">Página Inicial</span><span class="sxs-lookup"><span data-stu-id="a9464-146">Home</span></span>
          * <span data-ttu-id="a9464-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-147">Index.cshtml</span></span>

<span data-ttu-id="a9464-148">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="a9464-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="a9464-149">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="a9464-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="a9464-150">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="a9464-150">Associate the controller with an Area</span></span>

<span data-ttu-id="a9464-151">Os controladores de área são designados com o atributo [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="a9464-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="a9464-152">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="a9464-152">Add Area route</span></span>

<span data-ttu-id="a9464-153">As rotas de área normalmente usam  [Roteamento convencional](xref:mvc/controllers/routing#cr) em vez de [Roteamento de atributos](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="a9464-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="a9464-154">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="a9464-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a9464-155">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="a9464-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="a9464-156">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="a9464-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="a9464-157">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="a9464-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="a9464-158">Usando `{area:...}` com `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="a9464-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="a9464-159">É o mecanismo menos complicado para adicionar roteamento a áreas.</span><span class="sxs-lookup"><span data-stu-id="a9464-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="a9464-160">Corresponde a todos os controladores com o `[Area("Area name")]` atributo.</span><span class="sxs-lookup"><span data-stu-id="a9464-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="a9464-161">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="a9464-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="a9464-162">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="a9464-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="a9464-163">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="a9464-163">Link generation with MVC areas</span></span>

<span data-ttu-id="a9464-164">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="a9464-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="a9464-165">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém:</span><span class="sxs-lookup"><span data-stu-id="a9464-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="a9464-166">Os links anteriores.</span><span class="sxs-lookup"><span data-stu-id="a9464-166">The preceding links.</span></span>
* <span data-ttu-id="a9464-167">Links semelhantes aos anteriores, exceto `area` não são especificados.</span><span class="sxs-lookup"><span data-stu-id="a9464-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="a9464-168">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="a9464-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="a9464-169">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="a9464-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="a9464-170">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do [ambiente](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="a9464-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="a9464-171">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="a9464-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="a9464-172">Em muitos casos para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos com a marcação que não especifica a área.</span><span class="sxs-lookup"><span data-stu-id="a9464-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="a9464-173">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="a9464-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="a9464-174">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="a9464-175">Para compartilhar um layout comum para todo o aplicativo, mantenha o *_ViewStart. cshtml* na [pasta raiz do aplicativo](#arf).</span><span class="sxs-lookup"><span data-stu-id="a9464-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="a9464-176">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a9464-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="a9464-177">Pasta raiz do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a9464-177">Application root folder</span></span>

<span data-ttu-id="a9464-178">A pasta raiz do aplicativo é a pasta que contém *Startup.cs* no aplicativo Web criado com os modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9464-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="a9464-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="a9464-180">*/Views/_ViewImports. cshtml* , para MVC e */pages/_ViewImports. cshtml* para :::no-loc(Razor)::: páginas, não é importado para modos de exibição em áreas.</span><span class="sxs-lookup"><span data-stu-id="a9464-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="a9464-181">Use uma das abordagens a seguir para fornecer importações de exibição a todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="a9464-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="a9464-182">Adicione *_ViewImports. cshtml* à [pasta raiz do aplicativo](#arf).</span><span class="sxs-lookup"><span data-stu-id="a9464-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="a9464-183">Um *_ViewImports. cshtml* na pasta raiz do aplicativo será aplicado a todas as exibições no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="a9464-184">Copie o arquivo *_ViewImports. cshtml* para a pasta de exibição apropriada em áreas.</span><span class="sxs-lookup"><span data-stu-id="a9464-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="a9464-185">O arquivo *_ViewImports. cshtml* normalmente contém instruções de [marcação de auxiliares](xref:mvc/views/tag-helpers/intro) , `@using` e `@inject` .</span><span class="sxs-lookup"><span data-stu-id="a9464-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="a9464-186">Para obter mais informações, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="a9464-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="a9464-187">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="a9464-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="a9464-188">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="a9464-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="a9464-189">Áreas com :::no-loc(Razor)::: páginas</span><span class="sxs-lookup"><span data-stu-id="a9464-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="a9464-190">Áreas com :::no-loc(Razor)::: páginas exigem uma `Areas/<area name>/Pages` pasta na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="a9464-191">A seguinte estrutura de pasta é usada com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="a9464-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="a9464-192">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="a9464-192">Project name</span></span>
  * <span data-ttu-id="a9464-193">Áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-193">Areas</span></span>
    * <span data-ttu-id="a9464-194">Produtos</span><span class="sxs-lookup"><span data-stu-id="a9464-194">Products</span></span>
      * <span data-ttu-id="a9464-195">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="a9464-195">Pages</span></span>
        * <span data-ttu-id="a9464-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="a9464-196">_ViewImports</span></span>
        * <span data-ttu-id="a9464-197">Sobre</span><span class="sxs-lookup"><span data-stu-id="a9464-197">About</span></span>
        * <span data-ttu-id="a9464-198">Índice</span><span class="sxs-lookup"><span data-stu-id="a9464-198">Index</span></span>
    * <span data-ttu-id="a9464-199">Serviços</span><span class="sxs-lookup"><span data-stu-id="a9464-199">Services</span></span>
      * <span data-ttu-id="a9464-200">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="a9464-200">Pages</span></span>
        * <span data-ttu-id="a9464-201">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="a9464-201">Manage</span></span>
          * <span data-ttu-id="a9464-202">Sobre</span><span class="sxs-lookup"><span data-stu-id="a9464-202">About</span></span>
          * <span data-ttu-id="a9464-203">Índice</span><span class="sxs-lookup"><span data-stu-id="a9464-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="a9464-204">Geração de link com :::no-loc(Razor)::: páginas e áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="a9464-205">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="a9464-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="a9464-206">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="a9464-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="a9464-207">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="a9464-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="a9464-208">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="a9464-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="a9464-209">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="a9464-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="a9464-210">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="a9464-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="a9464-211">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="a9464-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="a9464-212">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a9464-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="a9464-213">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="a9464-213">For the preceding code:</span></span>

* <span data-ttu-id="a9464-214">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="a9464-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="a9464-215">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="a9464-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="a9464-216">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="a9464-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="a9464-217">O código vem do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="a9464-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="a9464-218">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="a9464-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="a9464-219">Um arquivo *_ViewImports. cshtml* pode ser adicionado a cada pasta de *páginas* de área para importar o namespace e os auxiliares de marca para cada :::no-loc(Razor)::: página na pasta.</span><span class="sxs-lookup"><span data-stu-id="a9464-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="a9464-220">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a9464-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="a9464-221">A marcação a seguir mostra a página */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="a9464-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="a9464-222">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="a9464-222">In the preceding markup:</span></span>

* <span data-ttu-id="a9464-223">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="a9464-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="a9464-224">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="a9464-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="a9464-225">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9464-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="a9464-226">A marcação a seguir mostra a página */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="a9464-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="a9464-227">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a9464-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="a9464-228">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="a9464-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="a9464-229">Layout compartilhado para :::no-loc(Razor)::: áreas de páginas</span><span class="sxs-lookup"><span data-stu-id="a9464-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="a9464-230">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="a9464-231">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-231">Publishing Areas</span></span>

<span data-ttu-id="a9464-232">Todos os arquivos \*.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="a9464-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9464-233">Áreas são um recurso do ASP.NET usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições).</span><span class="sxs-lookup"><span data-stu-id="a9464-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="a9464-234">O uso de áreas cria uma hierarquia para fins de roteamento adicionando outro parâmetro de rota, `area` , para `controller` e `action` ou uma :::no-loc(Razor)::: página `page` .</span><span class="sxs-lookup"><span data-stu-id="a9464-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="a9464-235">As áreas fornecem uma maneira de particionar um aplicativo Web ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de :::no-loc(Razor)::: páginas, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="a9464-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="a9464-236">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="a9464-237">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="a9464-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="a9464-238">O runtime do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="a9464-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="a9464-239">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="a9464-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="a9464-240">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="a9464-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="a9464-241">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, :::no-loc(Razor)::: páginas e modelos.</span><span class="sxs-lookup"><span data-stu-id="a9464-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="a9464-242">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="a9464-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="a9464-243">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="a9464-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="a9464-244">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="a9464-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="a9464-245">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a9464-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="a9464-246">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="a9464-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="a9464-247">Se você estiver usando :::no-loc(Razor)::: páginas, consulte [áreas com :::no-loc(Razor)::: páginas](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="a9464-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="a9464-248">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="a9464-248">Areas for controllers with views</span></span>

<span data-ttu-id="a9464-249">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a9464-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="a9464-250">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="a9464-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="a9464-251">Controladores com o [`[Area]`](#attribute) atributo para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="a9464-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="a9464-252">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="a9464-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="a9464-253">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="a9464-253">Area folder structure</span></span>

<span data-ttu-id="a9464-254">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços* .</span><span class="sxs-lookup"><span data-stu-id="a9464-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="a9464-255">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="a9464-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="a9464-256">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="a9464-256">Project name</span></span>
  * <span data-ttu-id="a9464-257">Áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-257">Areas</span></span>
    * <span data-ttu-id="a9464-258">Produtos</span><span class="sxs-lookup"><span data-stu-id="a9464-258">Products</span></span>
      * <span data-ttu-id="a9464-259">Controladores</span><span class="sxs-lookup"><span data-stu-id="a9464-259">Controllers</span></span>
        * <span data-ttu-id="a9464-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="a9464-260">HomeController.cs</span></span>
        * <span data-ttu-id="a9464-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="a9464-261">ManageController.cs</span></span>
      * <span data-ttu-id="a9464-262">Exibições</span><span class="sxs-lookup"><span data-stu-id="a9464-262">Views</span></span>
        * <span data-ttu-id="a9464-263">Página Inicial</span><span class="sxs-lookup"><span data-stu-id="a9464-263">Home</span></span>
          * <span data-ttu-id="a9464-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-264">Index.cshtml</span></span>
        * <span data-ttu-id="a9464-265">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="a9464-265">Manage</span></span>
          * <span data-ttu-id="a9464-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-266">Index.cshtml</span></span>
          * <span data-ttu-id="a9464-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-267">About.cshtml</span></span>
    * <span data-ttu-id="a9464-268">Serviços</span><span class="sxs-lookup"><span data-stu-id="a9464-268">Services</span></span>
      * <span data-ttu-id="a9464-269">Controladores</span><span class="sxs-lookup"><span data-stu-id="a9464-269">Controllers</span></span>
        * <span data-ttu-id="a9464-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="a9464-270">HomeController.cs</span></span>
      * <span data-ttu-id="a9464-271">Exibições</span><span class="sxs-lookup"><span data-stu-id="a9464-271">Views</span></span>
        * <span data-ttu-id="a9464-272">Página Inicial</span><span class="sxs-lookup"><span data-stu-id="a9464-272">Home</span></span>
          * <span data-ttu-id="a9464-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-273">Index.cshtml</span></span>

<span data-ttu-id="a9464-274">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="a9464-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="a9464-275">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="a9464-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="a9464-276">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="a9464-276">Associate the controller with an Area</span></span>

<span data-ttu-id="a9464-277">Os controladores de área são designados com o atributo [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="a9464-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="a9464-278">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="a9464-278">Add Area route</span></span>

<span data-ttu-id="a9464-279">Rotas de área normalmente usam o roteamento convencional, em vez de roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="a9464-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="a9464-280">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="a9464-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a9464-281">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="a9464-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="a9464-282">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="a9464-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="a9464-283">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="a9464-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="a9464-284">Usar `{area:...}` é o mecanismo menos complicado para a adição de roteamento para áreas.</span><span class="sxs-lookup"><span data-stu-id="a9464-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="a9464-285">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="a9464-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="a9464-286">Ao usar `MapAreaRoute` com o ASP.NET Core 2.2, veja [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="a9464-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="a9464-287">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="a9464-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="a9464-288">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="a9464-288">Link generation with MVC areas</span></span>

<span data-ttu-id="a9464-289">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="a9464-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="a9464-290">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="a9464-291">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="a9464-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="a9464-292">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="a9464-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="a9464-293">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="a9464-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="a9464-294">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="a9464-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="a9464-295">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="a9464-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="a9464-296">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="a9464-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="a9464-297">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="a9464-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="a9464-298">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="a9464-299">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="a9464-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="a9464-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="a9464-301">Em seu local padrão, */Views/_ViewImports.cshtml* não se aplica às áreas.</span><span class="sxs-lookup"><span data-stu-id="a9464-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="a9464-302">Para usar [auxiliares de marca](xref:mvc/views/tag-helpers/intro)comuns, `@using` ou `@inject` em sua área, verifique se um arquivo *_ViewImports. cshtml* adequado [se aplica às exibições de área](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="a9464-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="a9464-303">Se você quiser o mesmo comportamento em todas as suas exibições, mova */Views/_ViewImports.cshtml* para a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="a9464-304">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="a9464-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="a9464-305">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="a9464-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="a9464-306">Áreas com :::no-loc(Razor)::: páginas</span><span class="sxs-lookup"><span data-stu-id="a9464-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="a9464-307">Áreas com :::no-loc(Razor)::: páginas exigem uma `Areas/<area name>/Pages` pasta na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="a9464-308">A seguinte estrutura de pasta é usada com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="a9464-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="a9464-309">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="a9464-309">Project name</span></span>
  * <span data-ttu-id="a9464-310">Áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-310">Areas</span></span>
    * <span data-ttu-id="a9464-311">Produtos</span><span class="sxs-lookup"><span data-stu-id="a9464-311">Products</span></span>
      * <span data-ttu-id="a9464-312">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="a9464-312">Pages</span></span>
        * <span data-ttu-id="a9464-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="a9464-313">_ViewImports</span></span>
        * <span data-ttu-id="a9464-314">Sobre</span><span class="sxs-lookup"><span data-stu-id="a9464-314">About</span></span>
        * <span data-ttu-id="a9464-315">Índice</span><span class="sxs-lookup"><span data-stu-id="a9464-315">Index</span></span>
    * <span data-ttu-id="a9464-316">Serviços</span><span class="sxs-lookup"><span data-stu-id="a9464-316">Services</span></span>
      * <span data-ttu-id="a9464-317">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="a9464-317">Pages</span></span>
        * <span data-ttu-id="a9464-318">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="a9464-318">Manage</span></span>
          * <span data-ttu-id="a9464-319">Sobre</span><span class="sxs-lookup"><span data-stu-id="a9464-319">About</span></span>
          * <span data-ttu-id="a9464-320">Índice</span><span class="sxs-lookup"><span data-stu-id="a9464-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="a9464-321">Geração de link com :::no-loc(Razor)::: páginas e áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="a9464-322">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="a9464-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="a9464-323">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="a9464-324">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="a9464-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="a9464-325">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="a9464-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="a9464-326">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="a9464-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="a9464-327">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="a9464-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="a9464-328">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="a9464-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="a9464-329">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="a9464-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="a9464-330">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a9464-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="a9464-331">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="a9464-331">For the preceding code:</span></span>

* <span data-ttu-id="a9464-332">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="a9464-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="a9464-333">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="a9464-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="a9464-334">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="a9464-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="a9464-335">O código vem do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="a9464-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="a9464-336">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="a9464-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="a9464-337">Um arquivo *_ViewImports. cshtml* pode ser adicionado a cada pasta de *páginas* de área para importar o namespace e os auxiliares de marca para cada :::no-loc(Razor)::: página na pasta.</span><span class="sxs-lookup"><span data-stu-id="a9464-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="a9464-338">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a9464-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="a9464-339">A marcação a seguir mostra a página */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="a9464-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="a9464-340">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="a9464-340">In the preceding markup:</span></span>

* <span data-ttu-id="a9464-341">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="a9464-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="a9464-342">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="a9464-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="a9464-343">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9464-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="a9464-344">A marcação a seguir mostra a página */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="a9464-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="a9464-345">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a9464-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="a9464-346">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="a9464-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="a9464-347">Layout compartilhado para :::no-loc(Razor)::: áreas de páginas</span><span class="sxs-lookup"><span data-stu-id="a9464-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="a9464-348">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9464-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="a9464-349">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="a9464-349">Publishing Areas</span></span>

<span data-ttu-id="a9464-350">Todos os arquivos \*.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="a9464-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
