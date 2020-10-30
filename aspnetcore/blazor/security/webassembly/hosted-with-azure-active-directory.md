---
title: 'Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core hospedado com Azure Active Directory'
author: guardrex
description: 'Saiba como proteger um aplicativo ASP.NET Core :::no-loc(Blazor WebAssembly)::: hospedado com Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 3d0cc8d9891e0f656651a83dcd0bfdebfc266920
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055315"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="30a2a-103">Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core hospedado com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="30a2a-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory</span></span>

<span data-ttu-id="30a2a-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="30a2a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="30a2a-105">Este artigo descreve como criar um [ :::no-loc(Blazor WebAssembly)::: aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="30a2a-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="30a2a-106">Para :::no-loc(Blazor WebAssembly)::: aplicativos criados no Visual Studio que são configurados para dar suporte a contas em um diretório organizacional do AAD, o Visual Studio não configura o aplicativo corretamente na geração do projeto.</span><span class="sxs-lookup"><span data-stu-id="30a2a-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="30a2a-107">Isso será abordado em uma versão futura do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="30a2a-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="30a2a-108">Este artigo mostra como criar o aplicativo com o comando do CLI do .NET Core `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="30a2a-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="30a2a-109">Se você preferir criar o aplicativo com o Visual Studio antes de o IDE ser atualizado para os :::no-loc(Blazor)::: modelos mais recentes no ASP.NET Core 5,0, consulte cada seção deste artigo e confirme ou atualize a configuração do aplicativo após o Visual Studio criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="30a2a-110">Registrar aplicativos no AAD e criar a solução</span><span class="sxs-lookup"><span data-stu-id="30a2a-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="30a2a-111">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="30a2a-111">Create a tenant</span></span>

<span data-ttu-id="30a2a-112">Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.</span><span class="sxs-lookup"><span data-stu-id="30a2a-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="30a2a-113">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="30a2a-113">Register a server API app</span></span>

<span data-ttu-id="30a2a-114">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor* e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="30a2a-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="30a2a-115">Em **Azure Active Directory**  >  **registros de aplicativo** , selecione **novo registro** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-115">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="30a2a-116">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-116">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="30a2a-117">Escolha um **tipo de conta com suporte** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-117">Choose a **Supported account types** .</span></span> <span data-ttu-id="30a2a-118">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="30a2a-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="30a2a-119">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="30a2a-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="30a2a-120">Desmarque a caixa de seleção **permissões**  >  **conceder consentimento de administrador às permissões OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="30a2a-121">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-121">Select **Register** .</span></span>

<span data-ttu-id="30a2a-122">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="30a2a-122">Record the following information:</span></span>

* <span data-ttu-id="30a2a-123">*Aplicativo de API do servidor* ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="30a2a-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="30a2a-124">ID do diretório (locatário) (por exemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="30a2a-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="30a2a-125">Domínio principal/Publicador/locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="30a2a-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="30a2a-126">Em **permissões de API** , remova a permissão **Microsoft Graph**  >  **User. Read** , pois o aplicativo não requer acesso de entrada ou perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="30a2a-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="30a2a-127">No **expor uma API** :</span><span class="sxs-lookup"><span data-stu-id="30a2a-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="30a2a-128">Selecione **Adicionar um escopo** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-128">Select **Add a scope** .</span></span>
1. <span data-ttu-id="30a2a-129">Selecione **Salvar e continuar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-129">Select **Save and continue** .</span></span>
1. <span data-ttu-id="30a2a-130">Forneça um **nome de escopo** (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="30a2a-131">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="30a2a-132">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="30a2a-133">Confirme se o **estado** está definido como **habilitado** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-133">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="30a2a-134">Selecione **Adicionar escopo** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-134">Select **Add scope** .</span></span>

<span data-ttu-id="30a2a-135">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="30a2a-135">Record the following information:</span></span>

* <span data-ttu-id="30a2a-136">URI da ID do aplicativo (por exemplo,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou o valor personalizado que você fornece)</span><span class="sxs-lookup"><span data-stu-id="30a2a-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="30a2a-137">Nome do escopo (por exemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="30a2a-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="30a2a-138">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="30a2a-138">Register a client app</span></span>

<span data-ttu-id="30a2a-139">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *`Client`* aplicativo e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="30a2a-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="30a2a-140">Em **Azure Active Directory** > **registros de aplicativo** , selecione **novo registro** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-140">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="30a2a-141">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor)::: cliente AAD** ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-141">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="30a2a-142">Escolha um **tipo de conta com suporte** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-142">Choose a **Supported account types** .</span></span> <span data-ttu-id="30a2a-143">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="30a2a-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="30a2a-144">Defina a lista suspensa **URI de redirecionamento** para o **aplicativo de página única (Spa)** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="30a2a-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="30a2a-145">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="30a2a-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="30a2a-146">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="30a2a-147">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="30a2a-148">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="30a2a-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="30a2a-149">Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="30a2a-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="30a2a-150">Desmarque a caixa de seleção **permissões** > **conceder consentimento de administrador às permissões OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="30a2a-151">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-151">Select **Register** .</span></span>

<span data-ttu-id="30a2a-152">Registre a *`Client`* ID do aplicativo (cliente) de aplicativos (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="30a2a-153">Em configurações de plataforma de **autenticação** > **Platform configurations** > **, um aplicativo de página única (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="30a2a-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="30a2a-154">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="30a2a-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="30a2a-155">Para **concessão implícita** , verifique se as caixas de seleção para **tokens de acesso** e **tokens de ID** **não** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="30a2a-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="30a2a-156">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="30a2a-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="30a2a-157">Selecione o botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="30a2a-158">Em **Azure Active Directory** > **registros de aplicativo** , selecione **novo registro** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-158">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="30a2a-159">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor)::: cliente AAD** ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-159">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="30a2a-160">Escolha um **tipo de conta com suporte** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-160">Choose a **Supported account types** .</span></span> <span data-ttu-id="30a2a-161">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="30a2a-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="30a2a-162">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="30a2a-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="30a2a-163">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="30a2a-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="30a2a-164">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="30a2a-165">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="30a2a-166">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="30a2a-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="30a2a-167">Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="30a2a-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="30a2a-168">Desmarque a caixa de seleção **permissões** > **conceder consentimento de administrador às permissões OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="30a2a-169">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-169">Select **Register** .</span></span>

<span data-ttu-id="30a2a-170">Registre a *`Client`* ID do aplicativo (cliente) de aplicativos (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="30a2a-171">Em **Authentication** > **configurações da plataforma** de autenticação > **Web** :</span><span class="sxs-lookup"><span data-stu-id="30a2a-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="30a2a-172">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="30a2a-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="30a2a-173">Para **concessão implícita** , marque as caixas de seleção para **tokens de acesso** e **tokens de ID** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="30a2a-174">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="30a2a-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="30a2a-175">Selecione o botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="30a2a-176">Em **permissões de API** :</span><span class="sxs-lookup"><span data-stu-id="30a2a-176">In **API permissions** :</span></span>

1. <span data-ttu-id="30a2a-177">Confirme se o aplicativo tem a permissão **Microsoft Graph**  >  **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="30a2a-178">Selecione **Adicionar uma permissão** seguida por **minhas APIs** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-178">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="30a2a-179">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-179">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="30a2a-180">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-180">Open the **API** list.</span></span>
1. <span data-ttu-id="30a2a-181">Habilite o acesso à API (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="30a2a-182">Escolha **Adicionar permissões** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-182">Select **Add permissions** .</span></span>
1. <span data-ttu-id="30a2a-183">Selecione o botão **conceder consentimento de administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="30a2a-184">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="30a2a-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="30a2a-185">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-185">Create the app</span></span>

<span data-ttu-id="30a2a-186">Em uma pasta vazia, substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="30a2a-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="30a2a-187">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="30a2a-187">Placeholder</span></span>                  | <span data-ttu-id="30a2a-188">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="30a2a-188">Azure portal name</span></span>                                     | <span data-ttu-id="30a2a-189">Exemplo</span><span class="sxs-lookup"><span data-stu-id="30a2a-189">Example</span></span>                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `:::no-loc(Blazor):::Sample`                               |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="30a2a-190">ID do aplicativo (cliente) para o *`Client`* aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="30a2a-191">Nome do escopo</span><span class="sxs-lookup"><span data-stu-id="30a2a-191">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="30a2a-192">ID do aplicativo (cliente) para o *aplicativo de API do servidor*</span><span class="sxs-lookup"><span data-stu-id="30a2a-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="30a2a-193">URI da ID de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-193">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="30a2a-194">Domínio primário/Publicador/locatário</span><span class="sxs-lookup"><span data-stu-id="30a2a-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | <span data-ttu-id="30a2a-195">ID do diretório (locatário)</span><span class="sxs-lookup"><span data-stu-id="30a2a-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

<span data-ttu-id="30a2a-196">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-196">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="30a2a-197">Uma alteração de configuração pode ser necessária ao usar um locatário do Azure com um domínio do Publicador não verificado, que é descrito na seção [configurações do aplicativo](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="30a2a-197">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="30a2a-198">Uma alteração de configuração pode ser necessária ao usar um locatário do Azure com um domínio do Publicador não verificado, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="30a2a-198">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="30a2a-199">No portal do Azure, o *`Client`* **URI de redirecionamento** de configuração de plataforma do aplicativo é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="30a2a-199">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="30a2a-200">Se o *`Client`* aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo de API do servidor* no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-200">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="30a2a-201">Se a porta não foi configurada anteriormente com a *`Client`* porta conhecida do aplicativo, retorne ao *`Client`* registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="30a2a-201">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="30a2a-202">*`Server`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-202">*`Server`* app configuration</span></span>

<span data-ttu-id="30a2a-203">*Esta seção pertence ao aplicativo da solução **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="30a2a-203">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="30a2a-204">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="30a2a-204">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="30a2a-205">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web com a :::no-loc(Identity)::: plataforma Microsoft é fornecido pelos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="30a2a-205">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft :::no-loc(Identity)::: Platform is provided by the following packages:</span></span>

* [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web)
* [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)

```xml
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="30a2a-206">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="30a2a-206">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="30a2a-207">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) pacote:</span><span class="sxs-lookup"><span data-stu-id="30a2a-207">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="30a2a-208">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="30a2a-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="30a2a-209">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="30a2a-209">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="30a2a-210">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="30a2a-210">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="30a2a-211">O <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> método configura os serviços para proteger a API Web com a :::no-loc(Identity)::: plataforma Microsoft v 2.0.</span><span class="sxs-lookup"><span data-stu-id="30a2a-211">The <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> method configures services to protect the web API with Microsoft :::no-loc(Identity)::: Platform v2.0.</span></span> <span data-ttu-id="30a2a-212">Esse método espera uma `AzureAd` seção na configuração do aplicativo com as configurações necessárias para inicializar as opções de autenticação.</span><span class="sxs-lookup"><span data-stu-id="30a2a-212">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="30a2a-213">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="30a2a-213">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="30a2a-214">O <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="30a2a-214">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="30a2a-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:</span><span class="sxs-lookup"><span data-stu-id="30a2a-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="30a2a-216">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="30a2a-216">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="30a2a-217">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="30a2a-217">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="30a2a-218">Usuário. :::no-loc(Identity)::: . Nomes</span><span class="sxs-lookup"><span data-stu-id="30a2a-218">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="30a2a-219">Por padrão, a *`Server`* API do aplicativo é preenchida `User.:::no-loc(Identity):::.Name` com o valor do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de declaração (por exemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-219">By default, the *`Server`* app API populates `User.:::no-loc(Identity):::.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="30a2a-220">Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="30a2a-220">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="30a2a-221">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-221">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="30a2a-222">O `:::no-loc(appsettings.json):::` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso:</span><span class="sxs-lookup"><span data-stu-id="30a2a-222">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="30a2a-223">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="30a2a-223">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="30a2a-224">O `:::no-loc(appsettings.json):::` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso:</span><span class="sxs-lookup"><span data-stu-id="30a2a-224">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="30a2a-225">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="30a2a-225">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="30a2a-226">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="30a2a-226">WeatherForecast controller</span></span>

<span data-ttu-id="30a2a-227">O controlador WeatherForecast ( *Controllers/WeatherForecastController. cs* ) expõe uma API protegida com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="30a2a-227">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="30a2a-228">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="30a2a-228">It's **important** to understand that:</span></span>

* <span data-ttu-id="30a2a-229">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="30a2a-229">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="30a2a-230">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo usado no :::no-loc(Blazor WebAssembly)::: aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="30a2a-230">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="30a2a-231">*`Client`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-231">*`Client`* app configuration</span></span>

<span data-ttu-id="30a2a-232">*Esta seção pertence ao aplicativo da solução **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="30a2a-232">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="30a2a-233">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="30a2a-233">Authentication package</span></span>

<span data-ttu-id="30a2a-234">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-234">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="30a2a-235">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="30a2a-235">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="30a2a-236">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="30a2a-236">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="30a2a-237">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="30a2a-237">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="30a2a-238">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-238">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="30a2a-239">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="30a2a-239">Authentication service support</span></span>

<span data-ttu-id="30a2a-240">O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="30a2a-240">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="30a2a-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="30a2a-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="30a2a-242">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="30a2a-242">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="30a2a-243">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote.</span><span class="sxs-lookup"><span data-stu-id="30a2a-243">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="30a2a-244">Esse método configura os serviços necessários para que o aplicativo interaja com o :::no-loc(Identity)::: provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="30a2a-244">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="30a2a-245">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="30a2a-245">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="30a2a-246">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-246">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="30a2a-247">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30a2a-247">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="30a2a-248">A configuração é fornecida pelo `wwwroot/:::no-loc(appsettings.json):::` arquivo:</span><span class="sxs-lookup"><span data-stu-id="30a2a-248">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="30a2a-249">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="30a2a-249">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="30a2a-250">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="30a2a-250">Access token scopes</span></span>

<span data-ttu-id="30a2a-251">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="30a2a-251">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="30a2a-252">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="30a2a-252">Included by default in the sign in request.</span></span>
* <span data-ttu-id="30a2a-253">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="30a2a-253">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="30a2a-254">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="30a2a-254">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="30a2a-255">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="30a2a-255">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="30a2a-256">Especifique escopos adicionais com `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="30a2a-256">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="30a2a-257">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="30a2a-257">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="30a2a-258">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="30a2a-258">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="30a2a-259">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="30a2a-259">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="30a2a-260">Modo de logon</span><span class="sxs-lookup"><span data-stu-id="30a2a-260">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="30a2a-261">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="30a2a-261">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="30a2a-262">Página de índice</span><span class="sxs-lookup"><span data-stu-id="30a2a-262">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="30a2a-263">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-263">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="30a2a-264">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="30a2a-264">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="30a2a-265">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="30a2a-265">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="30a2a-266">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="30a2a-266">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="30a2a-267">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="30a2a-267">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="30a2a-268">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="30a2a-268">Run the app</span></span>

<span data-ttu-id="30a2a-269">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="30a2a-269">Run the app from the Server project.</span></span> <span data-ttu-id="30a2a-270">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="30a2a-270">When using Visual Studio, either:</span></span>

* <span data-ttu-id="30a2a-271">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-271">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="30a2a-272">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="30a2a-272">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="30a2a-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="30a2a-273">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="30a2a-274">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="30a2a-274">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="30a2a-275">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="30a2a-275">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
