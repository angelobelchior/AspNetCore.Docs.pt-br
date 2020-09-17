---
title: Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio
author: rick-anderson
description: Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: ebfe89d68725d597d6975015bbf84414ff818829
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722904"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="dd3d7-103">Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d7-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="dd3d7-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dd3d7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="dd3d7-105">Consulte [publicar um aplicativo Web para Azure app serviço usando Visual Studio para Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) se você estiver trabalhando no MacOS.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="dd3d7-106">Para solucionar um problema de implantação do Serviço de Aplicativo, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="dd3d7-107">Configuração</span><span class="sxs-lookup"><span data-stu-id="dd3d7-107">Set up</span></span>

* <span data-ttu-id="dd3d7-108">Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="dd3d7-109">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="dd3d7-109">Create a web app</span></span>

<span data-ttu-id="dd3d7-110">Na página inicial do Visual Studio, selecione **Arquivo > Novo > Projeto...**</span><span class="sxs-lookup"><span data-stu-id="dd3d7-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Arquivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="dd3d7-112">Complete a caixa de diálogo **Novo Projeto**:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="dd3d7-113">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="dd3d7-114">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-114">Select **Next**.</span></span>

![Caixa de diálogo Novo Projeto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="dd3d7-116">Na caixa de diálogo **Novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="dd3d7-117">Selecione **aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-117">Select **Web Application**.</span></span>
* <span data-ttu-id="dd3d7-118">Selecione **alterar** em autenticação.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-118">Select **Change** under Authentication.</span></span>

![Caixa de diálogo novo ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="dd3d7-120">A caixa de diálogo **Mudar Autenticação** é exibida.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="dd3d7-121">Selecione **Contas de Usuário Individuais**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="dd3d7-122">Selecione **OK** para retornar para o **novo aplicativo Web ASP.NET Core**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Caixa de diálogo Nova autenticação da Web do ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="dd3d7-124">O Visual Studio cria a solução.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="dd3d7-125">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dd3d7-125">Run the app</span></span>

* <span data-ttu-id="dd3d7-126">Pressione CTRL+F5 para executar o projeto.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="dd3d7-127">Teste o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="dd3d7-127">Test the **Privacy** link.</span></span>

![Aplicativo Web aberto no Microsoft Edge no localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="dd3d7-129">Registrar um usuário</span><span class="sxs-lookup"><span data-stu-id="dd3d7-129">Register a user</span></span>

* <span data-ttu-id="dd3d7-130">Selecione **Registrar** e registre um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="dd3d7-131">Você pode usar um endereço de email fictício.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-131">You can use a fictitious email address.</span></span> <span data-ttu-id="dd3d7-132">Ao enviar, a página exibirá o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="dd3d7-133">*"Uma operação de banco de dados falhou ao processar a solicitação. A aplicação de migrações existentes para o contexto do BD de aplicativos pode resolver esse problema. "*</span><span class="sxs-lookup"><span data-stu-id="dd3d7-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="dd3d7-134">Selecione **Aplicar Migrações** e, depois que a página for atualizada, atualize a página.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![uma operação de banco de dados falhou ao processar a solicitação.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="dd3d7-137">O aplicativo exibe o email usado para registrar o novo usuário e um link de **logoff** .</span><span class="sxs-lookup"><span data-stu-id="dd3d7-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Aplicativo Web aberto no Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="dd3d7-140">Implantar o aplicativo no Azure</span><span class="sxs-lookup"><span data-stu-id="dd3d7-140">Deploy the app to Azure</span></span>

<span data-ttu-id="dd3d7-141">Clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **publicar...**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu contextual aberto com o link Publicar realçado](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="dd3d7-143">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="dd3d7-144">Selecione **Azure**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-144">Select **Azure**.</span></span>
* <span data-ttu-id="dd3d7-145">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-145">Select **Next**.</span></span>

![Caixa de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="dd3d7-147">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="dd3d7-148">Selecione **serviço de Azure app (Linux)**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="dd3d7-149">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-149">Select **Next**.</span></span>

![Diálogo de publicação: selecione serviço do Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="dd3d7-151">Na caixa de diálogo **publicar** , selecione **criar um novo serviço de Azure app...**</span><span class="sxs-lookup"><span data-stu-id="dd3d7-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Diálogo de publicação: selecione instância de serviço do Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="dd3d7-153">A caixa de diálogo **Criar serviço de aplicativo** é exibida:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="dd3d7-154">Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-154">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="dd3d7-155">Você pode manter esses nomes ou alterá-los.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="dd3d7-156">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-156">Select **Create**.</span></span>

![Criar caixa de diálogo do Serviço de Aplicativo](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="dd3d7-158">Após a conclusão da criação, a caixa de diálogo é fechada automaticamente e a caixa de diálogo **publicar** fica em foco novamente:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="dd3d7-159">A nova instância que acabou de ser criada é selecionada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="dd3d7-160">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-160">Select **Finish**.</span></span>

![Diálogo de publicação: selecione instância do serviço de aplicativo](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="dd3d7-162">Em seguida, você verá a página **Resumo do perfil de publicação** .</span><span class="sxs-lookup"><span data-stu-id="dd3d7-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="dd3d7-163">O Visual Studio detectou que este aplicativo requer um banco de dados SQL Server e está solicitando que você o configure.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="dd3d7-164">Selecione **Configurar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-164">Select **Configure**.</span></span>

![Página de resumo do perfil de publicação: configurar dependência de SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="dd3d7-166">A caixa de diálogo **Configurar dependência** é exibida:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="dd3d7-167">Selecione **banco de dados SQL do Azure**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="dd3d7-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-168">Select **Next**.</span></span>

![Caixa de diálogo Configurar dependência de SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="dd3d7-170">Na caixa de diálogo **configurar banco de dados SQL do Azure** , selecione **criar um banco de dados SQL**</span><span class="sxs-lookup"><span data-stu-id="dd3d7-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Caixa de diálogo Configurar banco de dados SQL do Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="dd3d7-172">O **criar banco de dados SQL do Azure** é exibido:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="dd3d7-173">Os campos **nome do banco de dados**, grupo de **recursos**, servidor de banco de dados e plano **do** serviço de **aplicativo** são preenchidos.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-173">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="dd3d7-174">Você pode manter esses valores ou alterá-los.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="dd3d7-175">Insira o **nome de usuário do administrador de banco de dados** e a senha do administrador do **banco** de dados para o servidor de **banco de dados** selecionado (Observe que a conta usada deve ter as permissões necessárias para criar o novo banco de dados SQL do</span><span class="sxs-lookup"><span data-stu-id="dd3d7-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="dd3d7-176">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-176">Select **Create**.</span></span>

![Caixa de diálogo novo banco de dados SQL do Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="dd3d7-178">Após a conclusão da criação, a caixa de diálogo será fechada automaticamente e a caixa de diálogo **configurar banco de dados SQL do Azure** será focalizada novamente:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="dd3d7-179">A nova instância que acabou de ser criada é selecionada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="dd3d7-180">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-180">Select **Next**.</span></span>

![Caixa de diálogo Configurar banco de dados SQL do Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="dd3d7-182">Na próxima etapa da caixa de diálogo **configurar banco de dados SQL do Azure** :</span><span class="sxs-lookup"><span data-stu-id="dd3d7-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="dd3d7-183">Insira os campos **nome de usuário da conexão de banco de dados** e senha de conexão de banco de **dados**</span><span class="sxs-lookup"><span data-stu-id="dd3d7-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="dd3d7-184">Estes são os detalhes que seu aplicativo usará para se conectar ao banco de dados em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="dd3d7-185">A prática recomendada é evitar o uso dos mesmos detalhes que o nome de usuário do administrador & senha usada na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="dd3d7-186">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-186">Select **Finish**.</span></span>

![Caixa de diálogo Configurar banco de dados SQL do Azure, detalhes da cadeia de conexão](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="dd3d7-188">Na página **Resumo do perfil de publicação** , selecione **configurações**:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-188">In the **Publish Profile summary** page select **Settings**:</span></span>

![Página de resumo do perfil de publicação: Editar configurações](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="dd3d7-190">Na página **Configurações** da caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="dd3d7-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="dd3d7-191">Expanda **bancos de dados** e marque **usar esta cadeia de conexão em tempo de execução**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="dd3d7-192">Expanda **Entity Framework migrações** e marque **aplicar esta migração ao publicar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="dd3d7-193">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-193">Select **Save**.</span></span> <span data-ttu-id="dd3d7-194">O Visual Studio retorna para a caixa de diálogo **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Caixa de diálogo Publicar: painel Configurações](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="dd3d7-196">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-196">Click **Publish**.</span></span> <span data-ttu-id="dd3d7-197">O Visual Studio publica seu aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="dd3d7-198">Quando a implantação for concluída, o aplicativo será aberto em um navegador.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-198">When the deployment completes, the app is opened in a browser.</span></span>

![Caixa de diálogo Publicar: painel Configurações](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="dd3d7-200">Atualizar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dd3d7-200">Update the app</span></span>

* <span data-ttu-id="dd3d7-201">Edite a página *pages/index. cshtml* Razor e altere seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="dd3d7-202">Por exemplo, você pode modificar o parágrafo para dizer "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="dd3d7-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="dd3d7-203">Selecione **publicar** na página de **Resumo do perfil de publicação** novamente.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Página de resumo do perfil de publicação](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="dd3d7-205">Depois que o aplicativo for publicado, verifique se as alterações feitas estão disponíveis no Azure.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Verifique se a tarefa está concluída](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="dd3d7-207">Limpar</span><span class="sxs-lookup"><span data-stu-id="dd3d7-207">Clean up</span></span>

<span data-ttu-id="dd3d7-208">Quando você concluir o teste do aplicativo, acesse o [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="dd3d7-209">Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-209">Select **Resource groups**, then select the resource group you created.</span></span>

![Portal do Azure: Grupos de Recursos no menu da barra lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="dd3d7-211">Na página **Grupos de recursos**, selecione **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-211">In the **Resource groups** page, select **Delete**.</span></span>

![Portal do Azure: página Grupos de Recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="dd3d7-213">Insira o nome do grupo de recursos e selecione **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="dd3d7-214">O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.</span><span class="sxs-lookup"><span data-stu-id="dd3d7-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="dd3d7-215">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="dd3d7-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="dd3d7-216">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dd3d7-216">Additional resources</span></span>

* <span data-ttu-id="dd3d7-217">Para o Visual Studio Code, confira [Perfis de publicação](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="dd3d7-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="dd3d7-218">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="dd3d7-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="dd3d7-219">Grupos de recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="dd3d7-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="dd3d7-220">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="dd3d7-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>