---
title: Aprenda a migrar do ASP.NET MVC para o ASP.NET Core MVC
author: wadepickett
description: Saiba como iniciar a migração de um projeto MVC do ASP.NET para ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051507"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="60b40-103">Migrar do ASP.NET MVC para o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="60b40-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="60b40-104">Este artigo mostra como iniciar a migração de um projeto MVC do ASP.NET para [ASP.NET Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="60b40-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="60b40-105">No processo, ele realça as alterações relacionadas do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="60b40-106">A migração do ASP.NET MVC é um processo de várias etapas.</span><span class="sxs-lookup"><span data-stu-id="60b40-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="60b40-107">Este artigo cobre:</span><span class="sxs-lookup"><span data-stu-id="60b40-107">This article covers:</span></span>

* <span data-ttu-id="60b40-108">Configuração inicial.</span><span class="sxs-lookup"><span data-stu-id="60b40-108">Initial setup.</span></span>
* <span data-ttu-id="60b40-109">Controladores básicos e exibições.</span><span class="sxs-lookup"><span data-stu-id="60b40-109">Basic controllers and views.</span></span>
* <span data-ttu-id="60b40-110">Conteúdo estático.</span><span class="sxs-lookup"><span data-stu-id="60b40-110">Static content.</span></span>
* <span data-ttu-id="60b40-111">Dependências do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="60b40-111">Client-side dependencies.</span></span>

<span data-ttu-id="60b40-112">Para migrar a configuração e o :::no-loc(Identity)::: código, consulte [migrar a configuração para ASP.NET Core](xref:migration/configuration) e [migrar a autenticação e :::no-loc(Identity)::: para ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="60b40-112">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60b40-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="60b40-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="60b40-114">Criar o projeto do MVC do ASP.NET do início</span><span class="sxs-lookup"><span data-stu-id="60b40-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="60b40-115">Crie um projeto MVC de exemplo do ASP.NET no Visual Studio para migrar:</span><span class="sxs-lookup"><span data-stu-id="60b40-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="60b40-116">No menu **arquivo** , selecione **novo** > **projeto** .</span><span class="sxs-lookup"><span data-stu-id="60b40-116">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="60b40-117">Selecione **aplicativo Web ASP.net (.NET Framework)** e, em seguida, selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next** .</span></span>
1. <span data-ttu-id="60b40-118">Nomeie o projeto *WebApp1* para que o namespace corresponda ao projeto de ASP.NET Core criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="60b40-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="60b40-119">Selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-119">Select **Create** .</span></span>
1. <span data-ttu-id="60b40-120">Selecione **MVC** e, em seguida, selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-120">Select **MVC** , and then select **Create** .</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="60b40-121">Criar o projeto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60b40-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="60b40-122">Crie uma nova solução com um novo projeto ASP.NET Core para migrar para o:</span><span class="sxs-lookup"><span data-stu-id="60b40-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="60b40-123">Inicie uma segunda instância do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="60b40-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="60b40-124">No menu **arquivo** , selecione **novo** > **projeto** .</span><span class="sxs-lookup"><span data-stu-id="60b40-124">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="60b40-125">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-125">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>
1. <span data-ttu-id="60b40-126">Na caixa de diálogo **configurar seu novo projeto** , nomeie o projeto *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="60b40-126">In the **Configure your new project** dialog, Name the project *WebApp1* .</span></span>
1. <span data-ttu-id="60b40-127">Defina o local para um diretório diferente do projeto anterior para usar o mesmo nome de projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="60b40-128">Usar o mesmo namespace torna mais fácil copiar o código entre os dois projetos.</span><span class="sxs-lookup"><span data-stu-id="60b40-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="60b40-129">Selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-129">Select **Create** .</span></span>
1. <span data-ttu-id="60b40-130">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="60b40-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="60b40-131">Selecione o modelo de projeto **aplicativo Web (modelo-exibição-controlador)** e selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create** .</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="60b40-132">Configurar o site de ASP.NET Core para usar o MVC</span><span class="sxs-lookup"><span data-stu-id="60b40-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="60b40-133">Nos projetos ASP.NET Core 3,0 e posteriores, .NET Framework não é mais uma estrutura de destino com suporte.</span><span class="sxs-lookup"><span data-stu-id="60b40-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="60b40-134">Seu projeto deve ter como destino o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-134">Your project must target .NET Core.</span></span> <span data-ttu-id="60b40-135">A estrutura compartilhada ASP.NET Core, que inclui o MVC, faz parte da instalação do tempo de execução do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="60b40-136">A estrutura compartilhada é automaticamente referenciada ao usar o `Microsoft.NET.Sdk.Web` SDK no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="60b40-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="60b40-137">Para obter mais informações, consulte [referência de estrutura](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="60b40-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="60b40-138">Em ASP.NET Core, a `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="60b40-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="60b40-139">Substitui *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="60b40-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="60b40-140">Manipula todas as tarefas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="60b40-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="60b40-141">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="60b40-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="60b40-142">No ASP.NET Core projeto, abra o arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="60b40-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="60b40-143">ASP.NET Core aplicativos devem optar por recursos de estrutura com middleware.</span><span class="sxs-lookup"><span data-stu-id="60b40-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="60b40-144">O código gerado pelo modelo anterior adiciona os seguintes serviços e middleware:</span><span class="sxs-lookup"><span data-stu-id="60b40-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="60b40-145">O <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> método de extensão registra o suporte ao serviço MVC para controladores, recursos relacionados à API e modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="60b40-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="60b40-146">Para obter mais informações sobre as opções de registro do serviço MVC, consulte [registro de serviço do MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="60b40-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="60b40-147">O <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método de extensão adiciona o manipulador de arquivo estático `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="60b40-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="60b40-148">O `UseStaticFiles` método de extensão deve ser chamado antes de `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="60b40-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="60b40-149">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="60b40-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="60b40-150">O <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> método de extensão adiciona o roteamento.</span><span class="sxs-lookup"><span data-stu-id="60b40-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="60b40-151">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="60b40-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="60b40-152">Essa configuração existente inclui o que é necessário para migrar o projeto de exemplo do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="60b40-153">Para obter mais informações sobre ASP.NET Core opções de middleware, consulte <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="60b40-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="60b40-154">Migrar controladores e exibições</span><span class="sxs-lookup"><span data-stu-id="60b40-154">Migrate controllers and views</span></span>

<span data-ttu-id="60b40-155">No projeto ASP.NET Core, uma nova classe de controlador vazia e uma classe de exibição seriam adicionadas para servir como espaços reservados usando os mesmos nomes que o controlador e as classes de exibição em qualquer projeto MVC ASP.NET para o qual migrar.</span><span class="sxs-lookup"><span data-stu-id="60b40-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="60b40-156">O projeto ASP.NET Core *WebApp1* já inclui um controlador de exemplo mínimo e uma exibição com o mesmo nome que o projeto MVC do ASP.net.</span><span class="sxs-lookup"><span data-stu-id="60b40-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="60b40-157">Portanto, elas servirão como espaços reservados para o controlador MVC ASP.NET e exibições a serem migradas do projeto ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="60b40-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="60b40-158">Copie os métodos do ASP.NET MVC `HomeController` para substituir os novos métodos de ASP.NET Core `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="60b40-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="60b40-159">Não é necessário alterar o tipo de retorno dos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="60b40-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="60b40-160">O tipo de retorno do método de ação do controlador do modelo interno do ASP.NET MVC é <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; em ASP.NET Core MVC, os métodos de ação retornam `IActionResult` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="60b40-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="60b40-161">`ActionResult` implementa `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="60b40-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="60b40-162">No projeto ASP.NET Core, clique com o botão direito do mouse no diretório de *modos de exibição/início* , selecione **Adicionar** > **Item existente** .</span><span class="sxs-lookup"><span data-stu-id="60b40-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="60b40-163">Na caixa de diálogo **Adicionar item existente** , navegue até o diretório *base e os modos de exibição* do projeto ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="60b40-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="60b40-164">Selecione os arquivos de exibição *about. cshtml* , *Contact. cshtml* e *index. cshtml* e, :::no-loc(Razor)::: em seguida, selecione **Adicionar** , substituindo os arquivos existentes.</span><span class="sxs-lookup"><span data-stu-id="60b40-164">Select the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files, then select **Add** , replacing the existing files.</span></span>

<span data-ttu-id="60b40-165">Para obter mais informações, consulte <xref:mvc/controllers/actions> e <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="60b40-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="60b40-166">Testar cada método</span><span class="sxs-lookup"><span data-stu-id="60b40-166">Test each method</span></span>

<span data-ttu-id="60b40-167">Cada ponto de extremidade do controlador pode ser testado, no entanto, layout e estilos são abordados posteriormente no documento.</span><span class="sxs-lookup"><span data-stu-id="60b40-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="60b40-168">Execute o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="60b40-169">Invoque as exibições renderizadas do navegador no aplicativo ASP.NET Core em execução substituindo o número da porta atual pelo número da porta usado no projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="60b40-170">Por exemplo, `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="60b40-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="60b40-171">Migrar conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="60b40-171">Migrate static content</span></span>

<span data-ttu-id="60b40-172">No ASP.NET MVC 5 e versões anteriores, o conteúdo estático era hospedado no diretório raiz do projeto Web e foi misturado com arquivos do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="60b40-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="60b40-173">No ASP.NET Core, os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="60b40-174">O diretório padrão é *{Content root}/wwwroot* , mas pode ser alterado.</span><span class="sxs-lookup"><span data-stu-id="60b40-174">The default directory is *{content root}/wwwroot* , but it can be changed.</span></span> <span data-ttu-id="60b40-175">Saiba mais em [Arquivos estáticos no ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="60b40-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="60b40-176">Copie o conteúdo estático do projeto ASP.NET MVC *WebApp1* para o diretório *wwwroot* no projeto ASP.NET Core *WebApp1* :</span><span class="sxs-lookup"><span data-stu-id="60b40-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="60b40-177">No projeto ASP.NET Core, clique com o botão direito do mouse no diretório *wwwroot* , selecione **Adicionar** > **Item existente** .</span><span class="sxs-lookup"><span data-stu-id="60b40-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="60b40-178">Na caixa de diálogo **Adicionar item existente** , navegue até o projeto ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="60b40-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="60b40-179">Selecione o arquivo *favicon. ico* e, em seguida, selecione **Adicionar** , substituindo o arquivo existente.</span><span class="sxs-lookup"><span data-stu-id="60b40-179">Select the *favicon.ico* file, then select **Add** , replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="60b40-180">Migrar os arquivos de layout</span><span class="sxs-lookup"><span data-stu-id="60b40-180">Migrate the layout files</span></span>

<span data-ttu-id="60b40-181">Copie os arquivos de layout de projeto do ASP.NET MVC para o projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="60b40-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="60b40-182">No projeto ASP.NET Core, clique com o botão direito do mouse no diretório *exibições* , selecione **Adicionar** > **Item existente** .</span><span class="sxs-lookup"><span data-stu-id="60b40-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="60b40-183">Na caixa de diálogo **Adicionar item existente** , navegue até o diretório de *exibições* do projeto ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="60b40-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="60b40-184">Selecione o arquivo *_ViewStart. cshtml* e, em seguida, selecione **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="60b40-184">Select the *_ViewStart.cshtml* file then select **Add** .</span></span>

<span data-ttu-id="60b40-185">Copie os arquivos de layout compartilhado do projeto ASP.NET MVC para o projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="60b40-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="60b40-186">No projeto ASP.NET Core, clique com o botão direito do mouse no diretório *views/Shared* , selecione **Add** > **Existing Item** .</span><span class="sxs-lookup"><span data-stu-id="60b40-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="60b40-187">Na caixa de diálogo **Adicionar item existente** , navegue até o diretório *compartilhado/exibições* do projeto ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="60b40-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="60b40-188">Selecione o arquivo *_Layout. cshtml* e, em seguida, selecione **Adicionar** , substituindo o arquivo existente.</span><span class="sxs-lookup"><span data-stu-id="60b40-188">Select the *_Layout.cshtml* file, then select **Add** , replacing the existing file.</span></span>

<span data-ttu-id="60b40-189">No ASP.NET Core projeto, abra o arquivo *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="60b40-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="60b40-190">Faça as seguintes alterações para corresponder ao código concluído mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="60b40-191">Atualize a inclusão de CSS de Bootstrap para corresponder ao código concluído abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="60b40-192">Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="60b40-193">Remova `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="60b40-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="60b40-194">A marcação de substituição completa para a inclusão de CSS de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="60b40-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="60b40-195">Atualize o jQuery e a inclusão de JavaScript de inicialização para corresponder ao código completo abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="60b40-196">Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="60b40-197">Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="60b40-198">A marcação de substituição completa para jQuery e inclusão de JavaScript de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="60b40-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="60b40-199">O arquivo *_Layout. cshtml* atualizado é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="60b40-200">Exibir o site no navegador.</span><span class="sxs-lookup"><span data-stu-id="60b40-200">View the site in the browser.</span></span> <span data-ttu-id="60b40-201">Ele deve ser renderizado com os estilos esperados no lugar.</span><span class="sxs-lookup"><span data-stu-id="60b40-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="60b40-202">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="60b40-202">Configure bundling and minification</span></span>

<span data-ttu-id="60b40-203">ASP.NET Core é compatível com várias soluções de agrupamento e minificação de software livre, como [weboptimizer](https://github.com/ligershark/WebOptimizer) e outras bibliotecas semelhantes.</span><span class="sxs-lookup"><span data-stu-id="60b40-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="60b40-204">ASP.NET Core não fornece uma solução de agrupamento nativo e minificação.</span><span class="sxs-lookup"><span data-stu-id="60b40-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="60b40-205">Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="60b40-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="60b40-206">Resolver erros HTTP 500</span><span class="sxs-lookup"><span data-stu-id="60b40-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="60b40-207">Há muitos problemas que podem causar uma mensagem de erro HTTP 500 que não contém informações sobre a origem do problema.</span><span class="sxs-lookup"><span data-stu-id="60b40-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="60b40-208">Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista no projeto, um erro http 500 será gerado.</span><span class="sxs-lookup"><span data-stu-id="60b40-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="60b40-209">Por padrão, em aplicativos ASP.NET Core, a `UseDeveloperExceptionPage` extensão é adicionada ao `IApplicationBuilder` e executada quando o ambiente está em *desenvolvimento* .</span><span class="sxs-lookup"><span data-stu-id="60b40-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development* .</span></span> <span data-ttu-id="60b40-210">Isso é detalhado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="60b40-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="60b40-211">ASP.NET Core converte exceções sem tratamento em respostas de erro HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="60b40-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="60b40-212">Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor.</span><span class="sxs-lookup"><span data-stu-id="60b40-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="60b40-213">Para obter mais informações, consulte a [página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="60b40-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="60b40-214">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="60b40-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="60b40-215">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60b40-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="60b40-216">Este artigo mostra como iniciar a migração de um projeto ASP.NET MVC para [ASP.NET Core MVC](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="60b40-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="60b40-217">No processo, ele realça muitas das coisas que foram alteradas do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="60b40-218">A migração do ASP.NET MVC é um processo de várias etapas.</span><span class="sxs-lookup"><span data-stu-id="60b40-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="60b40-219">Este artigo cobre:</span><span class="sxs-lookup"><span data-stu-id="60b40-219">This article covers:</span></span>

* <span data-ttu-id="60b40-220">Instalação inicial</span><span class="sxs-lookup"><span data-stu-id="60b40-220">Initial setup</span></span>
* <span data-ttu-id="60b40-221">Controladores básicos e exibições</span><span class="sxs-lookup"><span data-stu-id="60b40-221">Basic controllers and views</span></span>
* <span data-ttu-id="60b40-222">Conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="60b40-222">Static content</span></span>
* <span data-ttu-id="60b40-223">Dependências do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="60b40-223">Client-side dependencies.</span></span>

<span data-ttu-id="60b40-224">Para migrar a configuração e o :::no-loc(Identity)::: código, consulte <xref:migration/configuration> e <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="60b40-224">For migrating configuration and :::no-loc(Identity)::: code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="60b40-225">Os números de versão nos exemplos podem não ser atuais, atualizar os projetos adequadamente.</span><span class="sxs-lookup"><span data-stu-id="60b40-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="60b40-226">Criar o projeto do MVC do ASP.NET do início</span><span class="sxs-lookup"><span data-stu-id="60b40-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="60b40-227">Para demonstrar a atualização, começaremos criando um aplicativo MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="60b40-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="60b40-228">Crie-o com o nome *WebApp1* para que o namespace corresponda ao projeto ASP.NET Core criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="60b40-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Caixa de diálogo Novo Projeto do Visual Studio](mvc/_static/new-project.png)

![Caixa de diálogo novo aplicativo Web: modelo de projeto MVC selecionado no painel modelos de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="60b40-231">*Opcional:* Altere o nome da solução de *WebApp1* para *Mvc5* .</span><span class="sxs-lookup"><span data-stu-id="60b40-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="60b40-232">O Visual Studio exibe o nome da nova solução ( *Mvc5* ), o que torna mais fácil dizer a esse projeto do próximo projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-232">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="60b40-233">Criar o projeto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60b40-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="60b40-234">Crie um novo aplicativo Web *vazio* ASP.NET Core com o mesmo nome do projeto anterior ( *WebApp1* ) para que os namespaces nos dois projetos correspondam.</span><span class="sxs-lookup"><span data-stu-id="60b40-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="60b40-235">Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos.</span><span class="sxs-lookup"><span data-stu-id="60b40-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="60b40-236">Crie este projeto em um diretório diferente do projeto anterior para usar o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="60b40-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Caixa de diálogo novo aplicativo Web ASP.NET: modelo de projeto vazio selecionado no painel modelos de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="60b40-239">*Opcional:* Crie um novo aplicativo ASP.NET Core usando o modelo de projeto de *aplicativo Web* .</span><span class="sxs-lookup"><span data-stu-id="60b40-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="60b40-240">Nomeie o projeto *WebApp1* e selecione uma opção de autenticação de **contas de usuário individuais** .</span><span class="sxs-lookup"><span data-stu-id="60b40-240">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="60b40-241">Renomeie este aplicativo como *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="60b40-241">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="60b40-242">A criação desse projeto poupa tempo na conversão.</span><span class="sxs-lookup"><span data-stu-id="60b40-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="60b40-243">O resultado final pode ser exibido no código gerado por modelo, o código pode ser copiado para o projeto de conversão ou comparado com o projeto gerado pelo modelo.</span><span class="sxs-lookup"><span data-stu-id="60b40-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="60b40-244">Configurar o site para usar o MVC</span><span class="sxs-lookup"><span data-stu-id="60b40-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="60b40-245">Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) é referenciado por padrão.</span><span class="sxs-lookup"><span data-stu-id="60b40-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="60b40-246">Este pacote contém pacotes comumente usados por aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="60b40-247">Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="60b40-248">`Microsoft.AspNetCore.Mvc` é o ASP.NET Core MVC Framework.</span><span class="sxs-lookup"><span data-stu-id="60b40-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="60b40-249">`Microsoft.AspNetCore.StaticFiles` é o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="60b40-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="60b40-250">ASP.NET Core aplicativos explicitamente optam pelo middleware, como para o fornecimento de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="60b40-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="60b40-251">Para obter mais informações, veja [Arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="60b40-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="60b40-252">Abra o arquivo *Startup.cs* e altere o código para corresponder ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="60b40-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="60b40-253">O <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensão adiciona o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="60b40-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="60b40-254">Para obter mais informações, consulte inicialização e [Roteamento](xref:fundamentals/routing)de [aplicativos](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="60b40-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="60b40-255">Adicionar um controlador e uma exibição</span><span class="sxs-lookup"><span data-stu-id="60b40-255">Add a controller and view</span></span>

<span data-ttu-id="60b40-256">Nesta seção, um controlador e uma exibição mínimas são adicionados para servir como espaços reservados para o controlador MVC ASP.NET e exibições migradas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="60b40-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="60b40-257">Adicione um diretório de *controladores* .</span><span class="sxs-lookup"><span data-stu-id="60b40-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="60b40-258">Adicione uma **classe de controlador** denominada *HomeController.cs* ao diretório de *controladores* .</span><span class="sxs-lookup"><span data-stu-id="60b40-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="60b40-260">Adicione um diretório de *exibições* .</span><span class="sxs-lookup"><span data-stu-id="60b40-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="60b40-261">Adicione um diretório *base views/* .</span><span class="sxs-lookup"><span data-stu-id="60b40-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="60b40-262">Adicione uma **:::no-loc(Razor)::: exibição** denominada *index. cshtml* ao diretório *base views/* .</span><span class="sxs-lookup"><span data-stu-id="60b40-262">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

<span data-ttu-id="60b40-264">A estrutura do projeto é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-264">The project structure is shown below:</span></span>

![Gerenciador de Soluções mostrando arquivos e diretórios de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="60b40-266">Substitua o conteúdo do arquivo *Views/Home/Index.cshtml* pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="60b40-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="60b40-267">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="60b40-267">Run the app.</span></span>

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="60b40-269">Para obter mais informações, consulte [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="60b40-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="60b40-270">A funcionalidade a seguir requer a migração do projeto ASP.NET MVC de exemplo para o projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="60b40-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="60b40-271">conteúdo do lado do cliente (CSS, fontes e scripts)</span><span class="sxs-lookup"><span data-stu-id="60b40-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="60b40-272">controladores</span><span class="sxs-lookup"><span data-stu-id="60b40-272">controllers</span></span>

* <span data-ttu-id="60b40-273">Modos de exibição</span><span class="sxs-lookup"><span data-stu-id="60b40-273">views</span></span>

* <span data-ttu-id="60b40-274">modelos</span><span class="sxs-lookup"><span data-stu-id="60b40-274">models</span></span>

* <span data-ttu-id="60b40-275">reagrupamento</span><span class="sxs-lookup"><span data-stu-id="60b40-275">bundling</span></span>

* <span data-ttu-id="60b40-276">filtros</span><span class="sxs-lookup"><span data-stu-id="60b40-276">filters</span></span>

* <span data-ttu-id="60b40-277">Faça logon/out, :::no-loc(Identity)::: (isso é feito no próximo tutorial.)</span><span class="sxs-lookup"><span data-stu-id="60b40-277">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="60b40-278">Controladores e exibições</span><span class="sxs-lookup"><span data-stu-id="60b40-278">Controllers and views</span></span>

* <span data-ttu-id="60b40-279">Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="60b40-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="60b40-280">No ASP.NET MVC, o tipo de retorno do método de ação do controlador interno do modelo é <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; em ASP.NET Core MVC, os métodos de ação retornam `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="60b40-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="60b40-281">`ActionResult` implementa `IActionResult` , portanto, não é necessário alterar o tipo de retorno dos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="60b40-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="60b40-282">Copie os arquivos de exibição *about. cshtml* , *Contact. cshtml* e *Index. cshtml* :::no-loc(Razor)::: do projeto ASP.NET MVC para o projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-282">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="60b40-283">Testar cada método</span><span class="sxs-lookup"><span data-stu-id="60b40-283">Test each method</span></span>

<span data-ttu-id="60b40-284">O arquivo de layout e os estilos ainda não foram migrados, portanto, as exibições renderizadas só contêm o conteúdo nos arquivos de exibição.</span><span class="sxs-lookup"><span data-stu-id="60b40-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="60b40-285">Os links gerados pelo arquivo de layout para as `About` `Contact` exibições e ainda não estarão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="60b40-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="60b40-286">Invoque as exibições renderizadas do navegador no aplicativo ASP.NET Core em execução, substituindo o número da porta atual pelo número da porta usada no projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="60b40-287">Por exemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="60b40-287">For example: `https://localhost:44375/home/about`.</span></span>

![Página de contato](mvc/_static/contact-page.png)

<span data-ttu-id="60b40-289">Observe a falta de estilos e itens de menu.</span><span class="sxs-lookup"><span data-stu-id="60b40-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="60b40-290">O estilo será corrigido na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="60b40-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="60b40-291">Conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="60b40-291">Static content</span></span>

<span data-ttu-id="60b40-292">No ASP.NET MVC 5 e versões anteriores, o conteúdo estático era hospedado da raiz do projeto Web e foi misturado com arquivos do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="60b40-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="60b40-293">No ASP.NET Core, o conteúdo estático é hospedado no diretório *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="60b40-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="60b40-294">Copie o conteúdo estático do aplicativo ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="60b40-295">Nesta conversão de exemplo:</span><span class="sxs-lookup"><span data-stu-id="60b40-295">In this sample conversion:</span></span>

* <span data-ttu-id="60b40-296">Copie o arquivo *favicon. ico* do projeto ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="60b40-297">O projeto MVC do ASP.NET usa a [inicialização](https://getbootstrap.com/) para seu estilo e armazena os arquivos de inicialização nos diretórios *conteúdo* e *scripts* .</span><span class="sxs-lookup"><span data-stu-id="60b40-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="60b40-298">O modelo, que gerou o projeto MVC do ASP.NET, faz referência à inicialização no arquivo de layout ( *views/Shared/_Layout. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="60b40-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="60b40-299">Os arquivos *bootstrap.js* e *bootstrap. css* podem ser COPIADOS do projeto MVC ASP.net para o diretório *wwwroot* no novo projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="60b40-300">Em vez disso, este documento adiciona suporte para bootstrap (e outras bibliotecas do lado do cliente) usando CDNs, na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="60b40-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="60b40-301">Migrar o arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="60b40-301">Migrate the layout file</span></span>

* <span data-ttu-id="60b40-302">Copie o arquivo *_ViewStart. cshtml* do diretório *views* do projeto ASP.NET MVC para o diretório de *exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="60b40-303">O arquivo *_ViewStart. cshtml* não foi alterado no ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="60b40-304">Crie um diretório *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="60b40-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="60b40-305">*Opcional:* Copie *_ViewImports. cshtml* do diretório *views* do projeto *FullAspNetCore* MVC para o diretório de *exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="60b40-306">Remova qualquer declaração de namespace no arquivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="60b40-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="60b40-307">O arquivo *_ViewImports. cshtml* fornece namespaces para todos os arquivos de exibição e apresenta [auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="60b40-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="60b40-308">Os auxiliares de marca são usados no novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="60b40-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="60b40-309">O arquivo *_ViewImports. cshtml* é novo para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="60b40-310">Copie o arquivo *_Layout. cshtml* do diretório *views/shared* do projeto ASP.NET MVC para o diretório *compartilhado/exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="60b40-311">Abra *_Layout arquivo. cshtml* e faça as seguintes alterações (o código completo é mostrado abaixo):</span><span class="sxs-lookup"><span data-stu-id="60b40-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="60b40-312">Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="60b40-313">Remova `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="60b40-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="60b40-314">Comente a `@Html.Partial("_LoginPartial")` linha (circundando a linha com `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="60b40-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="60b40-315">Para obter mais informações, consulte [migrar autenticação e :::no-loc(Identity)::: para ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="60b40-315">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="60b40-316">Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="60b40-317">Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="60b40-318">A marcação de substituição para inclusão de CSS de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="60b40-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="60b40-319">A marcação de substituição para a inclusão do jQuery e do JavaScript de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="60b40-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="60b40-320">O arquivo *_Layout. cshtml* atualizado é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="60b40-321">Exibir o site no navegador.</span><span class="sxs-lookup"><span data-stu-id="60b40-321">View the site in the browser.</span></span> <span data-ttu-id="60b40-322">Agora ele deve ser carregado corretamente, com os estilos esperados em vigor.</span><span class="sxs-lookup"><span data-stu-id="60b40-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="60b40-323">*Opcional:* Tente usar o novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="60b40-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="60b40-324">Copie o arquivo de layout do projeto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="60b40-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="60b40-325">O novo arquivo de layout usa [auxiliares de marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.</span><span class="sxs-lookup"><span data-stu-id="60b40-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="60b40-326">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="60b40-326">Configure bundling and minification</span></span>

<span data-ttu-id="60b40-327">Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="60b40-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="60b40-328">Resolver erros HTTP 500</span><span class="sxs-lookup"><span data-stu-id="60b40-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="60b40-329">Há muitos problemas que podem causar mensagens de erro HTTP 500 que não contêm informações sobre a origem do problema.</span><span class="sxs-lookup"><span data-stu-id="60b40-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="60b40-330">Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista no projeto, um erro http 500 será gerado.</span><span class="sxs-lookup"><span data-stu-id="60b40-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="60b40-331">Por padrão, em aplicativos ASP.NET Core, a `UseDeveloperExceptionPage` extensão é adicionada ao `IApplicationBuilder` e executada quando a configuração está em *desenvolvimento* .</span><span class="sxs-lookup"><span data-stu-id="60b40-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="60b40-332">Veja um exemplo no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="60b40-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="60b40-333">ASP.NET Core converte exceções sem tratamento em respostas de erro HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="60b40-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="60b40-334">Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor.</span><span class="sxs-lookup"><span data-stu-id="60b40-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="60b40-335">Para obter mais informações, consulte a [página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="60b40-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60b40-336">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60b40-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="60b40-337">Este artigo mostra como iniciar a migração de um projeto ASP.NET MVC para [ASP.NET Core MVC](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="60b40-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="60b40-338">No processo, ele realça muitas das coisas que foram alteradas do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="60b40-339">A migração do ASP.NET MVC é um processo de várias etapas.</span><span class="sxs-lookup"><span data-stu-id="60b40-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="60b40-340">Este artigo cobre:</span><span class="sxs-lookup"><span data-stu-id="60b40-340">This article covers:</span></span>

* <span data-ttu-id="60b40-341">Instalação inicial</span><span class="sxs-lookup"><span data-stu-id="60b40-341">Initial setup</span></span>
* <span data-ttu-id="60b40-342">Controladores básicos e exibições</span><span class="sxs-lookup"><span data-stu-id="60b40-342">Basic controllers and views</span></span>
* <span data-ttu-id="60b40-343">Conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="60b40-343">Static content</span></span>
* <span data-ttu-id="60b40-344">Dependências do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="60b40-344">Client-side dependencies.</span></span>

<span data-ttu-id="60b40-345">Para migrar a configuração e o :::no-loc(Identity)::: código, consulte [migrar a configuração para ASP.NET Core](xref:migration/configuration) e [migrar a autenticação e :::no-loc(Identity)::: para ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="60b40-345">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="60b40-346">Os números de versão nos exemplos podem não ser atuais, atualizar os projetos adequadamente.</span><span class="sxs-lookup"><span data-stu-id="60b40-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="60b40-347">Criar o projeto do MVC do ASP.NET do início</span><span class="sxs-lookup"><span data-stu-id="60b40-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="60b40-348">Para demonstrar a atualização, começaremos criando um aplicativo MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="60b40-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="60b40-349">Crie-o com o nome *WebApp1* para que o namespace corresponda ao projeto ASP.NET Core criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="60b40-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Caixa de diálogo Novo Projeto do Visual Studio](mvc/_static/new-project.png)

![Caixa de diálogo novo aplicativo Web: modelo de projeto MVC selecionado no painel modelos de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="60b40-352">*Opcional:* Altere o nome da solução de *WebApp1* para *Mvc5* .</span><span class="sxs-lookup"><span data-stu-id="60b40-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="60b40-353">O Visual Studio exibe o nome da nova solução ( *Mvc5* ), o que torna mais fácil dizer a esse projeto do próximo projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-353">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="60b40-354">Criar o projeto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60b40-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="60b40-355">Crie um novo aplicativo Web *vazio* ASP.NET Core com o mesmo nome do projeto anterior ( *WebApp1* ) para que os namespaces nos dois projetos correspondam.</span><span class="sxs-lookup"><span data-stu-id="60b40-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="60b40-356">Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos.</span><span class="sxs-lookup"><span data-stu-id="60b40-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="60b40-357">Crie este projeto em um diretório diferente do projeto anterior para usar o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="60b40-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Caixa de diálogo novo aplicativo Web ASP.NET: modelo de projeto vazio selecionado no painel modelos de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="60b40-360">*Opcional:* Crie um novo aplicativo ASP.NET Core usando o modelo de projeto de *aplicativo Web* .</span><span class="sxs-lookup"><span data-stu-id="60b40-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="60b40-361">Nomeie o projeto *WebApp1* e selecione uma opção de autenticação de **contas de usuário individuais** .</span><span class="sxs-lookup"><span data-stu-id="60b40-361">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="60b40-362">Renomeie este aplicativo como *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="60b40-362">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="60b40-363">A criação desse projeto poupa tempo na conversão.</span><span class="sxs-lookup"><span data-stu-id="60b40-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="60b40-364">O resultado final pode ser exibido no código gerado por modelo, o código pode ser copiado para o projeto de conversão ou comparado com o projeto gerado pelo modelo.</span><span class="sxs-lookup"><span data-stu-id="60b40-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="60b40-365">Configurar o site para usar o MVC</span><span class="sxs-lookup"><span data-stu-id="60b40-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="60b40-366">Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) é referenciado por padrão.</span><span class="sxs-lookup"><span data-stu-id="60b40-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="60b40-367">Este pacote contém pacotes comumente usados por aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="60b40-368">Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="60b40-369">`Microsoft.AspNetCore.Mvc` é o ASP.NET Core MVC Framework.</span><span class="sxs-lookup"><span data-stu-id="60b40-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="60b40-370">`Microsoft.AspNetCore.StaticFiles` é o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="60b40-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="60b40-371">ASP.NET Core aplicativos explicitamente optam pelo middleware, como para o fornecimento de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="60b40-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="60b40-372">Para obter mais informações, veja [Arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="60b40-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="60b40-373">Abra o arquivo *Startup.cs* e altere o código para corresponder ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="60b40-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="60b40-374">O <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensão adiciona o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="60b40-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="60b40-375">O `UseMvc` método de extensão adiciona o roteamento.</span><span class="sxs-lookup"><span data-stu-id="60b40-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="60b40-376">Para obter mais informações, consulte inicialização e [Roteamento](xref:fundamentals/routing)de [aplicativos](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="60b40-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="60b40-377">Adicionar um controlador e uma exibição</span><span class="sxs-lookup"><span data-stu-id="60b40-377">Add a controller and view</span></span>

<span data-ttu-id="60b40-378">Nesta seção, um controlador e uma exibição mínimas são adicionados para servir como espaços reservados para o controlador MVC ASP.NET e exibições migradas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="60b40-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="60b40-379">Adicione um diretório de *controladores* .</span><span class="sxs-lookup"><span data-stu-id="60b40-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="60b40-380">Adicione uma **classe de controlador** denominada *HomeController.cs* ao diretório de *controladores* .</span><span class="sxs-lookup"><span data-stu-id="60b40-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="60b40-382">Adicione um diretório de *exibições* .</span><span class="sxs-lookup"><span data-stu-id="60b40-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="60b40-383">Adicione um diretório *base views/* .</span><span class="sxs-lookup"><span data-stu-id="60b40-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="60b40-384">Adicione uma **:::no-loc(Razor)::: exibição** denominada *index. cshtml* ao diretório *base views/* .</span><span class="sxs-lookup"><span data-stu-id="60b40-384">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

<span data-ttu-id="60b40-386">A estrutura do projeto é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-386">The project structure is shown below:</span></span>

![Gerenciador de Soluções mostrando arquivos e diretórios de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="60b40-388">Substitua o conteúdo do arquivo *Views/Home/Index.cshtml* pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="60b40-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="60b40-389">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="60b40-389">Run the app.</span></span>

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="60b40-391">Para obter mais informações, consulte [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="60b40-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="60b40-392">A funcionalidade a seguir requer a migração do projeto ASP.NET MVC de exemplo para o projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="60b40-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="60b40-393">conteúdo do lado do cliente (CSS, fontes e scripts)</span><span class="sxs-lookup"><span data-stu-id="60b40-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="60b40-394">controladores</span><span class="sxs-lookup"><span data-stu-id="60b40-394">controllers</span></span>

* <span data-ttu-id="60b40-395">Modos de exibição</span><span class="sxs-lookup"><span data-stu-id="60b40-395">views</span></span>

* <span data-ttu-id="60b40-396">modelos</span><span class="sxs-lookup"><span data-stu-id="60b40-396">models</span></span>

* <span data-ttu-id="60b40-397">reagrupamento</span><span class="sxs-lookup"><span data-stu-id="60b40-397">bundling</span></span>

* <span data-ttu-id="60b40-398">filtros</span><span class="sxs-lookup"><span data-stu-id="60b40-398">filters</span></span>

* <span data-ttu-id="60b40-399">Faça logon/out, :::no-loc(Identity)::: (isso é feito no próximo tutorial.)</span><span class="sxs-lookup"><span data-stu-id="60b40-399">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="60b40-400">Controladores e exibições</span><span class="sxs-lookup"><span data-stu-id="60b40-400">Controllers and views</span></span>

* <span data-ttu-id="60b40-401">Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="60b40-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="60b40-402">No ASP.NET MVC, o tipo de retorno do método de ação do controlador interno do modelo é <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; em ASP.NET Core MVC, os métodos de ação retornam `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="60b40-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="60b40-403">`ActionResult` implementa `IActionResult` , portanto, não é necessário alterar o tipo de retorno dos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="60b40-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="60b40-404">Copie os arquivos de exibição *about. cshtml* , *Contact. cshtml* e *Index. cshtml* :::no-loc(Razor)::: do projeto ASP.NET MVC para o projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-404">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="60b40-405">Testar cada método</span><span class="sxs-lookup"><span data-stu-id="60b40-405">Test each method</span></span>

<span data-ttu-id="60b40-406">O arquivo de layout e os estilos ainda não foram migrados, portanto, as exibições renderizadas só contêm o conteúdo nos arquivos de exibição.</span><span class="sxs-lookup"><span data-stu-id="60b40-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="60b40-407">Os links gerados pelo arquivo de layout para as `About` `Contact` exibições e ainda não estarão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="60b40-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="60b40-408">Invoque as exibições renderizadas do navegador no aplicativo ASP.NET Core em execução, substituindo o número da porta atual pelo número da porta usada no projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="60b40-409">Por exemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="60b40-409">For example: `https://localhost:44375/home/about`.</span></span>

![Página de contato](mvc/_static/contact-page.png)

<span data-ttu-id="60b40-411">Observe a falta de estilos e itens de menu.</span><span class="sxs-lookup"><span data-stu-id="60b40-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="60b40-412">O estilo será corrigido na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="60b40-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="60b40-413">Conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="60b40-413">Static content</span></span>

<span data-ttu-id="60b40-414">No ASP.NET MVC 5 e versões anteriores, o conteúdo estático era hospedado da raiz do projeto Web e foi misturado com arquivos do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="60b40-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="60b40-415">No ASP.NET Core, o conteúdo estático é hospedado no diretório *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="60b40-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="60b40-416">Copie o conteúdo estático do aplicativo ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="60b40-417">Nesta conversão de exemplo:</span><span class="sxs-lookup"><span data-stu-id="60b40-417">In this sample conversion:</span></span>

* <span data-ttu-id="60b40-418">Copie o arquivo *favicon. ico* do projeto ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="60b40-419">O projeto MVC do ASP.NET usa a [inicialização](https://getbootstrap.com/) para seu estilo e armazena os arquivos de inicialização nos diretórios *conteúdo* e *scripts* .</span><span class="sxs-lookup"><span data-stu-id="60b40-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="60b40-420">O modelo, que gerou o projeto MVC do ASP.NET, faz referência à inicialização no arquivo de layout ( *views/Shared/_Layout. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="60b40-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="60b40-421">Os arquivos *bootstrap.js* e *bootstrap. css* podem ser COPIADOS do projeto MVC ASP.net para o diretório *wwwroot* no novo projeto.</span><span class="sxs-lookup"><span data-stu-id="60b40-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="60b40-422">Em vez disso, este documento adiciona suporte para bootstrap (e outras bibliotecas do lado do cliente) usando CDNs, na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="60b40-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="60b40-423">Migrar o arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="60b40-423">Migrate the layout file</span></span>

* <span data-ttu-id="60b40-424">Copie o arquivo *_ViewStart. cshtml* do diretório *views* do projeto ASP.NET MVC para o diretório de *exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="60b40-425">O arquivo *_ViewStart. cshtml* não foi alterado no ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="60b40-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="60b40-426">Crie um diretório *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="60b40-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="60b40-427">*Opcional:* Copie *_ViewImports. cshtml* do diretório *views* do projeto *FullAspNetCore* MVC para o diretório de *exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="60b40-428">Remova qualquer declaração de namespace no arquivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="60b40-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="60b40-429">O arquivo *_ViewImports. cshtml* fornece namespaces para todos os arquivos de exibição e apresenta [auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="60b40-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="60b40-430">Os auxiliares de marca são usados no novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="60b40-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="60b40-431">O arquivo *_ViewImports. cshtml* é novo para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="60b40-432">Copie o arquivo *_Layout. cshtml* do diretório *views/shared* do projeto ASP.NET MVC para o diretório *compartilhado/exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60b40-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="60b40-433">Abra *_Layout arquivo. cshtml* e faça as seguintes alterações (o código completo é mostrado abaixo):</span><span class="sxs-lookup"><span data-stu-id="60b40-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="60b40-434">Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="60b40-435">Remova `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="60b40-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="60b40-436">Comente a `@Html.Partial("_LoginPartial")` linha (circundando a linha com `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="60b40-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="60b40-437">Para obter mais informações, consulte [migrar autenticação e :::no-loc(Identity)::: para ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="60b40-437">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="60b40-438">Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="60b40-439">Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="60b40-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="60b40-440">A marcação de substituição para inclusão de CSS de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="60b40-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="60b40-441">A marcação de substituição para a inclusão do jQuery e do JavaScript de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="60b40-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="60b40-442">O arquivo *_Layout. cshtml* atualizado é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="60b40-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="60b40-443">Exibir o site no navegador.</span><span class="sxs-lookup"><span data-stu-id="60b40-443">View the site in the browser.</span></span> <span data-ttu-id="60b40-444">Agora ele deve ser carregado corretamente, com os estilos esperados em vigor.</span><span class="sxs-lookup"><span data-stu-id="60b40-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="60b40-445">*Opcional:* Tente usar o novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="60b40-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="60b40-446">Copie o arquivo de layout do projeto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="60b40-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="60b40-447">O novo arquivo de layout usa [auxiliares de marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.</span><span class="sxs-lookup"><span data-stu-id="60b40-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="60b40-448">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="60b40-448">Configure bundling and minification</span></span>

<span data-ttu-id="60b40-449">Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="60b40-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="60b40-450">Resolver erros HTTP 500</span><span class="sxs-lookup"><span data-stu-id="60b40-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="60b40-451">Há muitos problemas que podem causar mensagens de erro HTTP 500 que não contêm informações sobre a origem do problema.</span><span class="sxs-lookup"><span data-stu-id="60b40-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="60b40-452">Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista no projeto, um erro http 500 será gerado.</span><span class="sxs-lookup"><span data-stu-id="60b40-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="60b40-453">Por padrão, em aplicativos ASP.NET Core, a `UseDeveloperExceptionPage` extensão é adicionada ao `IApplicationBuilder` e executada quando a configuração está em *desenvolvimento* .</span><span class="sxs-lookup"><span data-stu-id="60b40-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="60b40-454">Veja um exemplo no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="60b40-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="60b40-455">ASP.NET Core converte exceções sem tratamento em respostas de erro HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="60b40-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="60b40-456">Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor.</span><span class="sxs-lookup"><span data-stu-id="60b40-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="60b40-457">Para obter mais informações, consulte a [página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="60b40-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60b40-458">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60b40-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
