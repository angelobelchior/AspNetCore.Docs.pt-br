---
title: Publicar um aplicativo ASP.NET Core no IIS
author: rick-anderson
description: Saiba como hospedar um aplicativo ASP.NET Core em um servidor IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060502"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="6464a-103">Publicar um aplicativo ASP.NET Core no IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="6464a-104">Este tutorial mostra como hospedar um aplicativo ASP.NET Core em um servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="6464a-105">Este tutorial cobre os seguintes assuntos:</span><span class="sxs-lookup"><span data-stu-id="6464a-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6464a-106">Instalar o Pacote de Hospedagem do .NET Core no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="6464a-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="6464a-107">Criar um site do IIS no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="6464a-108">Implantar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6464a-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6464a-109">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6464a-109">Prerequisites</span></span>

* <span data-ttu-id="6464a-110">[SDK do .NET Core](/dotnet/core/sdk) instalado no computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6464a-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="6464a-111">O Windows Server foi configurado com a função de servidor **Servidor Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="6464a-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="6464a-112">Se o servidor não estiver configurado para hospedar sites com o IIS, siga as orientações na seção *Configuração do IIS* do artigo <xref:host-and-deploy/iis/index#iis-configuration> e, em seguida, retorne a este tutorial.</span><span class="sxs-lookup"><span data-stu-id="6464a-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="6464a-113">**A configuração do IIS e a segurança do site envolvem conceitos que não são cobertos por este tutorial.**</span><span class="sxs-lookup"><span data-stu-id="6464a-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="6464a-114">Consulte as diretrizes do IIS na [documentação do IIS da Microsoft](https://www.iis.net/) e o [artigo ASP.NET Core sobre como hospedar com o IIS](xref:host-and-deploy/iis/index) antes de hospedar aplicativos de produção no IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="6464a-115">Os cenários importantes para a hospedagem do IIS não cobertos por este tutorial incluem:</span><span class="sxs-lookup"><span data-stu-id="6464a-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="6464a-116">Criação de um hive de Registro para proteção de dados de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="6464a-117">Configuração da ACL (lista de controle de acesso) do pool de aplicativos</span><span class="sxs-lookup"><span data-stu-id="6464a-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="6464a-118">Para se concentrar nos conceitos de implantação do IIS, este tutorial implanta um aplicativo sem segurança HTTPS configurada no IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="6464a-119">Para obter mais informações sobre como hospedar um aplicativo habilitado para o protocolo HTTPS, confira os tópicos de segurança na seção [Recursos adicionais](#additional-resources) deste artigo.</span><span class="sxs-lookup"><span data-stu-id="6464a-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="6464a-120">Mais diretrizes para hospedar aplicativos ASP.NET Core são apresentadas no <xref:host-and-deploy/iis/index> artigo.</span><span class="sxs-lookup"><span data-stu-id="6464a-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6464a-121">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6464a-122">Instalar o *Pacote de Hospedagem do .NET Core* no servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="6464a-123">O pacote instala o tempo de execução do .NET Core, a biblioteca do .NET Core e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="6464a-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6464a-124">O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="6464a-125">Baixe o instalador usando o seguinte link:</span><span class="sxs-lookup"><span data-stu-id="6464a-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="6464a-126">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="6464a-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="6464a-127">Execute o instalador no servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="6464a-128">Reinicie o servidor ou execute o `net stop was /y` seguido `net start w3svc` de em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="6464a-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="6464a-129">Criar o site do IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-129">Create the IIS site</span></span>

1. <span data-ttu-id="6464a-130">No servidor IIS, crie uma pasta para conter arquivos e pastas publicados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6464a-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="6464a-131">Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6464a-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="6464a-132">Para obter mais informações sobre o layout de arquivo e a pasta de implantação de um aplicativo, confira <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="6464a-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="6464a-133">No Gerenciador do IIS, abra o nó do servidor no painel **conexões** .</span><span class="sxs-lookup"><span data-stu-id="6464a-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="6464a-134">Clique com botão direito do mouse na pasta **Sites** .</span><span class="sxs-lookup"><span data-stu-id="6464a-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="6464a-135">Selecione **Adicionar Site** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="6464a-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="6464a-136">Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo que você criou.</span><span class="sxs-lookup"><span data-stu-id="6464a-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="6464a-137">Forneça a configuração **Associação** e crie o site ao selecionar **OK** .</span><span class="sxs-lookup"><span data-stu-id="6464a-137">Provide the **Binding** configuration and create the website by selecting **OK** .</span></span>

   > [!WARNING]
   > <span data-ttu-id="6464a-138">Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="6464a-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="6464a-139">Associações de curinga de nível superior podem abrir o aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="6464a-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="6464a-140">Isso se aplica a curingas fortes e fracos.</span><span class="sxs-lookup"><span data-stu-id="6464a-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="6464a-141">Use nomes de host explícitos em vez de curingas.</span><span class="sxs-lookup"><span data-stu-id="6464a-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="6464a-142">Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não têm esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="6464a-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6464a-143">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6464a-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="6464a-144">Confirme se a identidade do modelo de processo tem as permissões apropriadas.</span><span class="sxs-lookup"><span data-stu-id="6464a-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="6464a-145">Se a identidade padrão do pool de aplicativos ( **modelo de processo**  >  **:::no-loc(Identity):::** ) for alterada de `ApplicationPool:::no-loc(Identity):::` para outra identidade, verifique se a nova identidade tem as permissões necessárias para acessar a pasta, o banco de dados e outros recursos necessários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6464a-145">If the default identity of the app pool ( **Process Model** > **:::no-loc(Identity):::** ) is changed from `ApplicationPool:::no-loc(Identity):::` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="6464a-146">Por exemplo, o pool de aplicativos requer acesso de leitura e gravação às pastas nas quais o aplicativo lê e grava os arquivos.</span><span class="sxs-lookup"><span data-stu-id="6464a-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="6464a-147">Criar um :::no-loc(Razor)::: aplicativo de páginas ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-147">Create an ASP.NET Core :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="6464a-148">Siga o <xref:getting-started> tutorial para criar um :::no-loc(Razor)::: aplicativo de páginas.</span><span class="sxs-lookup"><span data-stu-id="6464a-148">Follow the <xref:getting-started> tutorial to create a :::no-loc(Razor)::: Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="6464a-149">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6464a-149">Publish and deploy the app</span></span>

<span data-ttu-id="6464a-150">*Publicar um aplicativo* significa produzir um aplicativo compilado que pode ser hospedado por um servidor.</span><span class="sxs-lookup"><span data-stu-id="6464a-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="6464a-151">*Implantar um aplicativo* significa mover o aplicativo publicado para um sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="6464a-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="6464a-152">A etapa de publicação é tratada pelo [SDK do .NET Core](/dotnet/core/sdk), enquanto a etapa de implantação pode ser tratada por diversas abordagens.</span><span class="sxs-lookup"><span data-stu-id="6464a-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="6464a-153">Este tutorial adota a abordagem de implantação de *pasta* , em que:</span><span class="sxs-lookup"><span data-stu-id="6464a-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="6464a-154">O aplicativo é publicado em uma pasta.</span><span class="sxs-lookup"><span data-stu-id="6464a-154">The app is published to a folder.</span></span>
* <span data-ttu-id="6464a-155">O conteúdo da pasta é movido para a pasta do site do IIS (o **caminho físico** para o site no Gerenciador do IIS).</span><span class="sxs-lookup"><span data-stu-id="6464a-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6464a-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6464a-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6464a-157">Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções** , e selecione **Publicar** .</span><span class="sxs-lookup"><span data-stu-id="6464a-157">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>
1. <span data-ttu-id="6464a-158">Na caixa de diálogo **Escolher um destino de publicação** , selecione a opção de publicação de **Pasta** .</span><span class="sxs-lookup"><span data-stu-id="6464a-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="6464a-159">Defina o caminho **Pasta ou Compartilhamento de arquivo** .</span><span class="sxs-lookup"><span data-stu-id="6464a-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="6464a-160">Se você criou uma pasta para o site do IIS que está disponível no computador de desenvolvimento como um compartilhamento de rede, forneça o caminho para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="6464a-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="6464a-161">O usuário atual deve ter acesso de gravação para publicar no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="6464a-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="6464a-162">Se não for possível implantar diretamente na pasta do site do IIS no servidor IIS, publique em uma pasta em mídia removível e mova fisicamente o aplicativo publicado para a pasta do site do IIS no servidor, que é o **caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="6464a-163">Mova o conteúdo da `bin/Release/{TARGET FRAMEWORK}/publish` pasta para a pasta do site do IIS no servidor, que é o **caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6464a-164">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-164">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="6464a-165">Em um shell de comando, publique o aplicativo na Configuração de versão usando o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="6464a-165">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="6464a-166">Mova o conteúdo da `bin/Release/{TARGET FRAMEWORK}/publish` pasta para a pasta do site do IIS no servidor, que é o **caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-166">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6464a-167">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6464a-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6464a-168">Clique com o botão direito do mouse no projeto em **Solução** e selecione **Publicar** > **Publicar na Pasta** .</span><span class="sxs-lookup"><span data-stu-id="6464a-168">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder** .</span></span>
1. <span data-ttu-id="6464a-169">Defina o caminho **Escolher uma pasta** .</span><span class="sxs-lookup"><span data-stu-id="6464a-169">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="6464a-170">Se você criou uma pasta para o site do IIS que está disponível no computador de desenvolvimento como um compartilhamento de rede, forneça o caminho para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="6464a-170">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="6464a-171">O usuário atual deve ter acesso de gravação para publicar no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="6464a-171">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="6464a-172">Se não for possível implantar diretamente na pasta do site do IIS no servidor IIS, publique em uma pasta na mídia removida e mova fisicamente o aplicativo publicado para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no IIS Manager.</span><span class="sxs-lookup"><span data-stu-id="6464a-172">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="6464a-173">Mova o conteúdo da `bin/Release/{TARGET FRAMEWORK}/publish` pasta para a pasta do site do IIS no servidor, que é o **caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-173">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="6464a-174">Procurar no site</span><span class="sxs-lookup"><span data-stu-id="6464a-174">Browse the website</span></span>

<span data-ttu-id="6464a-175">O aplicativo pode ser acessado em um navegador depois de receber a primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="6464a-175">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="6464a-176">Faça uma solicitação para o aplicativo na associação de ponto de extremidade que você estabeleceu no Gerenciador do IIS para o site.</span><span class="sxs-lookup"><span data-stu-id="6464a-176">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6464a-177">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="6464a-177">Next steps</span></span>

<span data-ttu-id="6464a-178">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="6464a-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6464a-179">Instalar o Pacote de Hospedagem do .NET Core no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="6464a-179">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="6464a-180">Criar um site do IIS no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="6464a-180">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="6464a-181">Implantar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6464a-181">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="6464a-182">Para saber mais sobre como hospedar aplicativos ASP.NET Core no IIS, confira o artigo Visão Geral do IIS:</span><span class="sxs-lookup"><span data-stu-id="6464a-182">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="6464a-183">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6464a-183">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="6464a-184">Artigos no conjunto de documentação do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-184">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="6464a-185">Artigos referentes à implantação do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-185">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="6464a-186">Publicar um aplicativo Web em uma pasta usando o Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6464a-186">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="6464a-187">Artigos sobre a configuração HTTPS do IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-187">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="6464a-188">Como configurar SSL no Gerenciador do IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-188">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="6464a-189">Como configurar o SSL no IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-189">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="6464a-190">Artigos sobre IIS e Windows Server</span><span class="sxs-lookup"><span data-stu-id="6464a-190">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="6464a-191">O site oficial da IIS da Microsoft</span><span class="sxs-lookup"><span data-stu-id="6464a-191">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="6464a-192">Biblioteca de conteúdo técnico do Windows Server</span><span class="sxs-lookup"><span data-stu-id="6464a-192">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="6464a-193">Recursos de implantação para administradores do IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-193">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="6464a-194">Documentação do ISS</span><span class="sxs-lookup"><span data-stu-id="6464a-194">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="6464a-195">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="6464a-195">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="6464a-196">Implantação de aplicativo .NET Core</span><span class="sxs-lookup"><span data-stu-id="6464a-196">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

