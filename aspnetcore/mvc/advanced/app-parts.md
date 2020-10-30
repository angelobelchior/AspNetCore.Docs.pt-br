---
title: 'Compartilhar controladores, exibições, :::no-loc(Razor)::: páginas e muito mais com partes de aplicativo no ASP.NET Core'
author: rick-anderson
description: 'Compartilhar controladores, exibir, :::no-loc(Razor)::: páginas e muito mais com partes de aplicativo no ASP.NET Core'
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061178"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="c01c9-103">Compartilhar controladores, exibições, :::no-loc(Razor)::: páginas e muito mais com partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="c01c9-103">Share controllers, views, :::no-loc(Razor)::: Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c01c9-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c01c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c01c9-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c01c9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c01c9-106">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="c01c9-107">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, :::no-loc(Razor)::: páginas, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="c01c9-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, :::no-loc(Razor)::: Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="c01c9-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="c01c9-109">`AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="c01c9-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="c01c9-110">Os [provedores de recursos](#fp) trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c01c9-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="c01c9-111">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="c01c9-112">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c01c9-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="c01c9-113">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, :::no-loc(Razor)::: páginas, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c01c9-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, :::no-loc(Razor)::: Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="c01c9-114">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="c01c9-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="c01c9-115">Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="c01c9-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="c01c9-116">A <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="c01c9-117">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c01c9-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="c01c9-118">Use as <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes e para descobrir e carregar ASP.NET Core recursos (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="c01c9-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="c01c9-119">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> rastreia as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="c01c9-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="c01c9-120">`ApplicationPartManager` está configurado em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c01c9-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="c01c9-121">O código a seguir fornece uma abordagem alternativa para configurar o `ApplicationPartManager` usando `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="c01c9-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="c01c9-122">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="c01c9-123">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="c01c9-124">Consulte o download de exemplo da [solução WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="c01c9-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="c01c9-125">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="c01c9-125">Include views</span></span>

<span data-ttu-id="c01c9-126">Use uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class) para incluir exibições no assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-126">Use a [:::no-loc(Razor)::: class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="c01c9-127">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="c01c9-127">Prevent loading resources</span></span>

<span data-ttu-id="c01c9-128">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="c01c9-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="c01c9-129">Adicionar ou remover membros da  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> coleção para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="c01c9-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="c01c9-130">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="c01c9-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="c01c9-131">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="c01c9-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="c01c9-132">Por exemplo, configure o `ApplicationPartManager` antes de chamar `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="c01c9-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="c01c9-133">Chame `Remove` na `ApplicationParts` coleção para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="c01c9-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="c01c9-134">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="c01c9-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="c01c9-135">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="c01c9-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.Compiled:::no-loc(Razor):::AssemblyPart`
* `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation`
* <span data-ttu-id="c01c9-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="c01c9-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="c01c9-137">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span><span class="sxs-lookup"><span data-stu-id="c01c9-137">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="c01c9-138">Provedores de recursos</span><span class="sxs-lookup"><span data-stu-id="c01c9-138">Feature providers</span></span>

<span data-ttu-id="c01c9-139">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="c01c9-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="c01c9-140">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c01c9-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="c01c9-141">`internal class`[ :::no-loc(Razor)::: CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.:::no-loc(Razor):::/src/ApplicationParts/:::no-loc(Razor):::CompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="c01c9-141">`internal class` [:::no-loc(Razor):::CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.:::no-loc(Razor):::/src/ApplicationParts/:::no-loc(Razor):::CompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="c01c9-142">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="c01c9-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="c01c9-143">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c01c9-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="c01c9-144">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c01c9-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="c01c9-145">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c01c9-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="c01c9-146">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="c01c9-146">Display available features</span></span>

<span data-ttu-id="c01c9-147">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` [injeção de dependência](../../fundamentals/dependency-injection.md)por meio do:</span><span class="sxs-lookup"><span data-stu-id="c01c9-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="c01c9-148">O [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c01c9-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="c01c9-149">Descoberta em partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="c01c9-149">Discovery in application parts</span></span>

<span data-ttu-id="c01c9-150">Os erros HTTP 404 não são incomuns ao desenvolver com partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="c01c9-151">Esses erros são geralmente causados por falta de um requisito essencial para o modo como as partes de aplicativos são descobertas.</span><span class="sxs-lookup"><span data-stu-id="c01c9-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="c01c9-152">Se seu aplicativo retornar um erro HTTP 404, verifique se os seguintes requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="c01c9-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="c01c9-153">A `applicationName` configuração precisa ser definida como o assembly raiz usado para descoberta.</span><span class="sxs-lookup"><span data-stu-id="c01c9-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="c01c9-154">O assembly raiz usado para descoberta normalmente é o assembly de ponto de entrada.</span><span class="sxs-lookup"><span data-stu-id="c01c9-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="c01c9-155">O assembly raiz precisa ter uma referência às partes usadas para descoberta.</span><span class="sxs-lookup"><span data-stu-id="c01c9-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="c01c9-156">A referência pode ser direta ou transitiva.</span><span class="sxs-lookup"><span data-stu-id="c01c9-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="c01c9-157">O assembly raiz precisa fazer referência ao SDK da Web.</span><span class="sxs-lookup"><span data-stu-id="c01c9-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="c01c9-158">A estrutura tem lógica que carimba os atributos no assembly raiz que são usados para descoberta.</span><span class="sxs-lookup"><span data-stu-id="c01c9-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c01c9-159">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c01c9-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c01c9-160">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c01c9-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c01c9-161">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="c01c9-162">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, :::no-loc(Razor)::: páginas, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="c01c9-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, :::no-loc(Razor)::: Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="c01c9-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="c01c9-164">`AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="c01c9-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="c01c9-165">Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c01c9-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="c01c9-166">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="c01c9-167">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c01c9-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="c01c9-168">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, :::no-loc(Razor)::: páginas, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c01c9-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, :::no-loc(Razor)::: Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="c01c9-169">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="c01c9-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="c01c9-170">Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="c01c9-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="c01c9-171">A <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="c01c9-172">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c01c9-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="c01c9-173">Use as `ApplicationPart` `AssemblyPart` classes e para descobrir e carregar ASP.NET Core recursos (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="c01c9-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="c01c9-174">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> rastreia as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="c01c9-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="c01c9-175">`ApplicationPartManager` está configurado em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c01c9-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="c01c9-176">O código a seguir fornece uma abordagem alternativa para configurar o `ApplicationPartManager` usando `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="c01c9-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="c01c9-177">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="c01c9-178">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="c01c9-179">Consulte o download de exemplo da [solução WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="c01c9-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="c01c9-180">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="c01c9-180">Include views</span></span>

<span data-ttu-id="c01c9-181">Use uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class) para incluir exibições no assembly.</span><span class="sxs-lookup"><span data-stu-id="c01c9-181">Use a [:::no-loc(Razor)::: class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="c01c9-182">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="c01c9-182">Prevent loading resources</span></span>

<span data-ttu-id="c01c9-183">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="c01c9-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="c01c9-184">Adicionar ou remover membros da  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> coleção para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="c01c9-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="c01c9-185">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="c01c9-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="c01c9-186">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="c01c9-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="c01c9-187">Por exemplo, configure o `ApplicationPartManager` antes de chamar `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="c01c9-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="c01c9-188">Chame `Remove` na `ApplicationParts` coleção para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="c01c9-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="c01c9-189">O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="c01c9-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="c01c9-190">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="c01c9-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="c01c9-191">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="c01c9-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="c01c9-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="c01c9-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="c01c9-193">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span><span class="sxs-lookup"><span data-stu-id="c01c9-193">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="c01c9-194">Provedores de recursos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="c01c9-194">Application feature providers</span></span>

<span data-ttu-id="c01c9-195">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="c01c9-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="c01c9-196">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c01c9-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="c01c9-197">Controladores</span><span class="sxs-lookup"><span data-stu-id="c01c9-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="c01c9-198">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="c01c9-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="c01c9-199">Exibir componentes</span><span class="sxs-lookup"><span data-stu-id="c01c9-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="c01c9-200">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="c01c9-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="c01c9-201">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c01c9-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="c01c9-202">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c01c9-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="c01c9-203">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c01c9-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="c01c9-204">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="c01c9-204">Display available features</span></span>

<span data-ttu-id="c01c9-205">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` [injeção de dependência](../../fundamentals/dependency-injection.md)por meio do:</span><span class="sxs-lookup"><span data-stu-id="c01c9-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="c01c9-206">O [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c01c9-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="c01c9-207">Descoberta em partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="c01c9-207">Discovery in application parts</span></span>

<span data-ttu-id="c01c9-208">Os erros HTTP 404 não são incomuns ao desenvolver com partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c01c9-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="c01c9-209">Esses erros são geralmente causados por falta de um requisito essencial para o modo como as partes de aplicativos são descobertas.</span><span class="sxs-lookup"><span data-stu-id="c01c9-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="c01c9-210">Se seu aplicativo retornar um erro HTTP 404, verifique se os seguintes requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="c01c9-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="c01c9-211">A `applicationName` configuração precisa ser definida como o assembly raiz usado para descoberta.</span><span class="sxs-lookup"><span data-stu-id="c01c9-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="c01c9-212">O assembly raiz usado para descoberta normalmente é o assembly de ponto de entrada.</span><span class="sxs-lookup"><span data-stu-id="c01c9-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="c01c9-213">O assembly raiz precisa ter uma referência às partes usadas para descoberta.</span><span class="sxs-lookup"><span data-stu-id="c01c9-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="c01c9-214">A referência pode ser direta ou transitiva.</span><span class="sxs-lookup"><span data-stu-id="c01c9-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="c01c9-215">O assembly raiz precisa fazer referência ao SDK da Web.</span><span class="sxs-lookup"><span data-stu-id="c01c9-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="c01c9-216">A estrutura de ASP.NET Core tem uma lógica de compilação personalizada que carimba atributos no assembly raiz que são usados para descoberta.</span><span class="sxs-lookup"><span data-stu-id="c01c9-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
