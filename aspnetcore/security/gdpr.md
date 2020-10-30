---
title: Suporte a Regulamento Geral sobre a Proteção de Dados (GDPR) no ASP.NET Core
author: rick-anderson
description: Saiba como acessar os pontos de extensão do GDPR em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050999"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="da1d0-103">Suporte a Regulamento Geral sobre a Proteção de Dados da UE (GDPR) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da1d0-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="da1d0-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da1d0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da1d0-105">ASP.NET Core fornece APIs e modelos para ajudar a atender a alguns dos requisitos de [regulamento geral sobre a proteção de dados da UE (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="da1d0-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="da1d0-106">Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir pela sua política de privacidade e :::no-loc(cookie)::: uso.</span><span class="sxs-lookup"><span data-stu-id="da1d0-106">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="da1d0-107">A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="da1d0-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="da1d0-108">Para habilitar o :::no-loc(cookie)::: recurso de consentimento padrão como o encontrado nos modelos ASP.NET Core 2,2 em um aplicativo gerado pelo modelo ASP.NET Core 3,0:</span><span class="sxs-lookup"><span data-stu-id="da1d0-108">To enable the default :::no-loc(cookie)::: consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="da1d0-109">Adicione `using Microsoft.AspNetCore.Http` à lista de diretivas using.</span><span class="sxs-lookup"><span data-stu-id="da1d0-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="da1d0-110">Adicione [ :::no-loc(Cookie)::: políticas](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) para `Startup.ConfigureServices` e [use a :::no-loc(Cookie)::: política](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) para `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="da1d0-110">Add [:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) to `Startup.ConfigureServices` and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="da1d0-111">Adicione o :::no-loc(cookie)::: consentimento parcial ao arquivo *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="da1d0-111">Add the :::no-loc(cookie)::: consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="da1d0-112">Adicione o arquivo *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* ao projeto:</span><span class="sxs-lookup"><span data-stu-id="da1d0-112">Add the *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

* <span data-ttu-id="da1d0-113">Selecione a versão ASP.NET Core 2,2 deste artigo para ler sobre o :::no-loc(cookie)::: recurso de consentimento.</span><span class="sxs-lookup"><span data-stu-id="da1d0-113">Select the ASP.NET Core 2.2 version of this article to read about the :::no-loc(cookie)::: consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="da1d0-114">Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir pela sua política de privacidade e :::no-loc(cookie)::: uso.</span><span class="sxs-lookup"><span data-stu-id="da1d0-114">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="da1d0-115">Um :::no-loc(cookie)::: recurso de consentimento permite que você solicite (e rastreie) o consentimento dos usuários para armazenar informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="da1d0-115">A :::no-loc(cookie)::: consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="da1d0-116">Se um usuário não consentiu na coleta de dados e o aplicativo tiver [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) definido como `true` , os s não essenciais :::no-loc(cookie)::: não serão enviados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="da1d0-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) set to `true`, non-essential :::no-loc(cookie):::s aren't sent to the browser.</span></span>
* <span data-ttu-id="da1d0-117">:::no-loc(Cookie):::os s podem ser marcados como essenciais.</span><span class="sxs-lookup"><span data-stu-id="da1d0-117">:::no-loc(Cookie):::s can be marked as essential.</span></span> <span data-ttu-id="da1d0-118">:::no-loc(cookie):::Os s essenciais são enviados ao navegador mesmo quando o usuário não consentiu e o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="da1d0-118">Essential :::no-loc(cookie):::s are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="da1d0-119">[TempData e Session :::no-loc(cookie)::: s](#tempdata) não são funcionais quando o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="da1d0-119">[TempData and Session :::no-loc(cookie):::s](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="da1d0-120">A página [ :::no-loc(Identity)::: gerenciar](#pd) fornece um link para baixar e excluir dados do usuário.</span><span class="sxs-lookup"><span data-stu-id="da1d0-120">The [:::no-loc(Identity)::: manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="da1d0-121">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite que você teste a maioria dos pontos de extensão GDPR e as APIs adicionadas aos modelos ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="da1d0-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="da1d0-122">Consulte o arquivo [Leiame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obter instruções de teste.</span><span class="sxs-lookup"><span data-stu-id="da1d0-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="da1d0-123">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da1d0-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="da1d0-124">ASP.NET Core suporte a GDPR no código gerado por modelo</span><span class="sxs-lookup"><span data-stu-id="da1d0-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="da1d0-125">:::no-loc(Razor)::: As páginas e os projetos MVC criados com os modelos de projeto incluem o seguinte suporte a GDPR:</span><span class="sxs-lookup"><span data-stu-id="da1d0-125">:::no-loc(Razor)::: Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="da1d0-126">[ :::no-loc(Cookie)::: Políticas](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) e políticas [de :::no-loc(Cookie)::: uso](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) são definidas na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="da1d0-126">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="da1d0-127">A [exibição parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)de *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="da1d0-127">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="da1d0-128">Um botão **aceitar** está incluído neste arquivo.</span><span class="sxs-lookup"><span data-stu-id="da1d0-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="da1d0-129">Quando o usuário clica no botão **aceitar** , o consentimento do armazenamento :::no-loc(cookie)::: s é fornecido.</span><span class="sxs-lookup"><span data-stu-id="da1d0-129">When the user clicks the **Accept** button, consent to store :::no-loc(cookie):::s is provided.</span></span>
* <span data-ttu-id="da1d0-130">A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="da1d0-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="da1d0-131">O arquivo *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* gera um link para a página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="da1d0-131">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="da1d0-132">Para aplicativos criados com contas de usuário individuais, a página Gerenciar fornece links para baixar e excluir [dados de usuário pessoal](#pd).</span><span class="sxs-lookup"><span data-stu-id="da1d0-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="da1d0-133">:::no-loc(Cookie):::Política de políticas e :::no-loc(Cookie)::: uso</span><span class="sxs-lookup"><span data-stu-id="da1d0-133">:::no-loc(Cookie):::PolicyOptions and Use:::no-loc(Cookie):::Policy</span></span>

<span data-ttu-id="da1d0-134">As [ :::no-loc(Cookie)::: políticas](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) são inicializadas em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da1d0-134">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="da1d0-135">[Usar :::no-loc(Cookie)::: o A política](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) é chamada em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="da1d0-135">[Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="da1d0-136">\_:::no-loc(Cookie):::Exibição parcial de ConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="da1d0-136">\_:::no-loc(Cookie):::ConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="da1d0-137">A exibição parcial de *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="da1d0-137">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

<span data-ttu-id="da1d0-138">Isso parcial:</span><span class="sxs-lookup"><span data-stu-id="da1d0-138">This partial:</span></span>

* <span data-ttu-id="da1d0-139">Obtém o estado do controle para o usuário.</span><span class="sxs-lookup"><span data-stu-id="da1d0-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="da1d0-140">Se o aplicativo estiver configurado para exigir consentimento, o usuário deverá consentir antes que os :::no-loc(cookie)::: s possam ser rastreados.</span><span class="sxs-lookup"><span data-stu-id="da1d0-140">If the app is configured to require consent, the user must consent before :::no-loc(cookie):::s can be tracked.</span></span> <span data-ttu-id="da1d0-141">Se o consentimento for necessário, o :::no-loc(cookie)::: painel de consentimento será corrigido na parte superior da barra de navegação criada pelo arquivo *\_ layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="da1d0-141">If consent is required, the :::no-loc(cookie)::: consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="da1d0-142">Fornece um `<p>` elemento HTML para resumir sua política de privacidade e :::no-loc(cookie)::: uso.</span><span class="sxs-lookup"><span data-stu-id="da1d0-142">Provides an HTML `<p>` element to summarize your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="da1d0-143">Fornece um link para a página de privacidade ou a exibição em que você pode detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="da1d0-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="da1d0-144">:::no-loc(cookie):::S essenciais</span><span class="sxs-lookup"><span data-stu-id="da1d0-144">Essential :::no-loc(cookie):::s</span></span>

<span data-ttu-id="da1d0-145">Se o consentimento para :::no-loc(cookie)::: o armazenamento não tiver sido fornecido, somente os :::no-loc(cookie)::: s marcados como essenciais serão enviados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="da1d0-145">If consent to store :::no-loc(cookie):::s hasn't been provided, only :::no-loc(cookie):::s marked essential are sent to the browser.</span></span> <span data-ttu-id="da1d0-146">O código a seguir torna um :::no-loc(cookie)::: essencial:</span><span class="sxs-lookup"><span data-stu-id="da1d0-146">The following code makes a :::no-loc(cookie)::: essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/:::no-loc(Cookie):::.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="da1d0-147">O provedor TempData e o estado de sessão :::no-loc(cookie)::: s não são essenciais</span><span class="sxs-lookup"><span data-stu-id="da1d0-147">TempData provider and session state :::no-loc(cookie):::s aren't essential</span></span>

<span data-ttu-id="da1d0-148">O [provedor TempData](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: não é essencial.</span><span class="sxs-lookup"><span data-stu-id="da1d0-148">The [TempData provider](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: isn't essential.</span></span> <span data-ttu-id="da1d0-149">Se o rastreamento estiver desabilitado, o provedor TempData não funcionará.</span><span class="sxs-lookup"><span data-stu-id="da1d0-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="da1d0-150">Para habilitar o provedor TempData quando o rastreamento estiver desabilitado, marque o TempData :::no-loc(cookie)::: como essencial em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da1d0-150">To enable the TempData provider when tracking is disabled, mark the TempData :::no-loc(cookie)::: as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="da1d0-151">[Estado](xref:fundamentals/app-state) :::no-loc(cookie)::: da sessão os s não são essenciais.</span><span class="sxs-lookup"><span data-stu-id="da1d0-151">[Session state](xref:fundamentals/app-state) :::no-loc(cookie):::s are not essential.</span></span> <span data-ttu-id="da1d0-152">O estado da sessão não é funcional quando o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="da1d0-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="da1d0-153">O código a seguir torna a sessão :::no-loc(cookie)::: s essencial:</span><span class="sxs-lookup"><span data-stu-id="da1d0-153">The following code makes session :::no-loc(cookie):::s essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="da1d0-154">Dados pessoais</span><span class="sxs-lookup"><span data-stu-id="da1d0-154">Personal data</span></span>

<span data-ttu-id="da1d0-155">ASP.NET Core aplicativos criados com contas de usuário individuais incluem código para baixar e excluir dados pessoais.</span><span class="sxs-lookup"><span data-stu-id="da1d0-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="da1d0-156">Selecione o nome de usuário e, em seguida, selecione **dados pessoais** :</span><span class="sxs-lookup"><span data-stu-id="da1d0-156">Select the user name and then select **Personal data** :</span></span>

![Página Gerenciar dados pessoais](gdpr/_static/pd.png)

<span data-ttu-id="da1d0-158">Observações:</span><span class="sxs-lookup"><span data-stu-id="da1d0-158">Notes:</span></span>

* <span data-ttu-id="da1d0-159">Para gerar o `Account/Manage` código, consulte [Scaffold :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="da1d0-159">To generate the `Account/Manage` code, see [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="da1d0-160">Os links de **exclusão** e **Download** só agem nos dados de identidade padrão.</span><span class="sxs-lookup"><span data-stu-id="da1d0-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="da1d0-161">Aplicativos que criam dados de usuário personalizados devem ser estendidos para excluir/baixar os dados de usuário personalizados.</span><span class="sxs-lookup"><span data-stu-id="da1d0-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="da1d0-162">Para obter mais informações, consulte [Adicionar, baixar e excluir dados de usuário personalizados :::no-loc(Identity)::: para ](xref:security/authentication/add-user-data)o.</span><span class="sxs-lookup"><span data-stu-id="da1d0-162">For more information, see [Add, download, and delete custom user data to :::no-loc(Identity):::](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="da1d0-163">Os tokens salvos para o usuário que são armazenados na :::no-loc(Identity)::: tabela de banco de dados `AspNetUserTokens` são excluídos quando o usuário é excluído por meio do comportamento de exclusão em cascata devido à [chave estrangeira](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="da1d0-163">Saved tokens for the user that are stored in the :::no-loc(Identity)::: database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152).</span></span>
* <span data-ttu-id="da1d0-164">A [autenticação de provedor externo](xref:security/authentication/social/index), como o Facebook e o Google, não está disponível antes que a :::no-loc(cookie)::: política seja aceita.</span><span class="sxs-lookup"><span data-stu-id="da1d0-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the :::no-loc(cookie)::: policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="da1d0-165">Criptografia em repouso</span><span class="sxs-lookup"><span data-stu-id="da1d0-165">Encryption at rest</span></span>

<span data-ttu-id="da1d0-166">Alguns bancos de dados e mecanismos de armazenamento permitem a criptografia em repouso.</span><span class="sxs-lookup"><span data-stu-id="da1d0-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="da1d0-167">Criptografia em repouso:</span><span class="sxs-lookup"><span data-stu-id="da1d0-167">Encryption at rest:</span></span>

* <span data-ttu-id="da1d0-168">Criptografa os dados armazenados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="da1d0-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="da1d0-169">Criptografa sem configuração, programação ou outro trabalho para o software que acessa os dados.</span><span class="sxs-lookup"><span data-stu-id="da1d0-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="da1d0-170">É a opção mais fácil e mais segura.</span><span class="sxs-lookup"><span data-stu-id="da1d0-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="da1d0-171">Permite que o banco de dados gerencie chaves e criptografia.</span><span class="sxs-lookup"><span data-stu-id="da1d0-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="da1d0-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="da1d0-172">For example:</span></span>

* <span data-ttu-id="da1d0-173">O Microsoft SQL e o Azure SQL fornecem [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="da1d0-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="da1d0-174">O SQL Azure criptografa o banco de dados por padrão</span><span class="sxs-lookup"><span data-stu-id="da1d0-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="da1d0-175">Os [BLOBs, arquivos, tabelas e armazenamento de fila do Azure são criptografados por padrão](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="da1d0-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="da1d0-176">Para bancos de dados que não fornecem criptografia interna em repouso, talvez você possa usar a criptografia de disco para fornecer a mesma proteção.</span><span class="sxs-lookup"><span data-stu-id="da1d0-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="da1d0-177">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="da1d0-177">For example:</span></span>

* [<span data-ttu-id="da1d0-178">BitLocker para Windows Server</span><span class="sxs-lookup"><span data-stu-id="da1d0-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="da1d0-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="da1d0-179">Linux:</span></span>
  * [<span data-ttu-id="da1d0-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="da1d0-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="da1d0-181">[Encfs](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="da1d0-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da1d0-182">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="da1d0-182">Additional resources</span></span>

* [<span data-ttu-id="da1d0-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="da1d0-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="da1d0-184">GDPR-adicionando um botão revogar consentimento no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da1d0-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
