---
title: 'Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core autônomo com Azure Active Directory B2C'
author: guardrex
description: 'Saiba como proteger um aplicativo ASP.NET Core :::no-loc(Blazor WebAssembly)::: autônomo com Azure Active Directory B2C.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 423c7ad9f00a991c634f24a170c52d6dcb035ee1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055146"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="75a0d-103">Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core autônomo com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="75a0d-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="75a0d-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75a0d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75a0d-105">Para criar um [ :::no-loc(Blazor WebAssembly)::: aplicativo autônomo](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="75a0d-105">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="75a0d-106">Siga as orientações nos tópicos a seguir para criar um locatário e registrar um aplicativo Web no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="75a0d-106">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="75a0d-107">Criar um locatário AAD B2C</span><span class="sxs-lookup"><span data-stu-id="75a0d-107">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="75a0d-108">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="75a0d-108">Record the following information:</span></span>

* <span data-ttu-id="75a0d-109">AAD B2C instância (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita): a instância é o esquema e o host de um registro de aplicativo do Azure B2C, que pode ser encontrado abrindo a janela **pontos de extremidade** na página **registros de aplicativo** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="75a0d-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="75a0d-110">AAD B2C domínio primário/Publicador/locatário (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="75a0d-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="75a0d-111">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *`Client`* aplicativo e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="75a0d-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="75a0d-112">Em **Azure Active Directory** > **registros de aplicativo** , selecione **novo registro** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-112">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="75a0d-113">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor)::: AAD B2C autônomo** ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-113">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="75a0d-114">Para **tipos de conta com suporte** , selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="75a0d-114">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="75a0d-115">Defina a lista suspensa **URI de redirecionamento** para o **aplicativo de página única (Spa)** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="75a0d-115">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="75a0d-116">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="75a0d-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="75a0d-117">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75a0d-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="75a0d-118">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="75a0d-119">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="75a0d-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="75a0d-120">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="75a0d-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="75a0d-121">Confirme se **as permissões** > **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="75a0d-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="75a0d-122">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-122">Select **Register** .</span></span>

<span data-ttu-id="75a0d-123">Registre a ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="75a0d-124">Em configurações de plataforma de **autenticação** > **Platform configurations** > **, um aplicativo de página única (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="75a0d-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="75a0d-125">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="75a0d-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="75a0d-126">Para **concessão implícita** , verifique se as caixas de seleção para **tokens de acesso** e **tokens de ID** **não** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="75a0d-126">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="75a0d-127">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="75a0d-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="75a0d-128">Selecione o botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="75a0d-129">Em **Azure Active Directory** > **registros de aplicativo** , selecione **novo registro** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-129">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="75a0d-130">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor)::: AAD B2C autônomo** ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-130">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="75a0d-131">Para **tipos de conta com suporte** , selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="75a0d-131">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="75a0d-132">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="75a0d-132">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="75a0d-133">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="75a0d-133">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="75a0d-134">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75a0d-134">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="75a0d-135">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-135">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="75a0d-136">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="75a0d-136">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="75a0d-137">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="75a0d-137">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="75a0d-138">Confirme se **as permissões** > **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="75a0d-138">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="75a0d-139">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-139">Select **Register** .</span></span>

<span data-ttu-id="75a0d-140">Registre a ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-140">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="75a0d-141">Em **Authentication** > **configurações da plataforma** de autenticação > **Web** :</span><span class="sxs-lookup"><span data-stu-id="75a0d-141">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="75a0d-142">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="75a0d-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="75a0d-143">Para **concessão implícita** , marque as caixas de seleção para **tokens de acesso** e **tokens de ID** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-143">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="75a0d-144">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="75a0d-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="75a0d-145">Selecione o botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="75a0d-146">Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário** :</span><span class="sxs-lookup"><span data-stu-id="75a0d-146">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="75a0d-147">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="75a0d-147">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="75a0d-148">No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >  **Display Name** para popular o `context.User.:::no-loc(Identity):::.Name` no `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-148">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.:::no-loc(Identity):::.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="75a0d-149">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-149">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="75a0d-150">Em uma pasta vazia, substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="75a0d-150">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="75a0d-151">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="75a0d-151">Placeholder</span></span>                   | <span data-ttu-id="75a0d-152">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="75a0d-152">Azure portal name</span></span>               | <span data-ttu-id="75a0d-153">Exemplo</span><span class="sxs-lookup"><span data-stu-id="75a0d-153">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="75a0d-154">Instância</span><span class="sxs-lookup"><span data-stu-id="75a0d-154">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `:::no-loc(Blazor):::Sample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="75a0d-155">ID do aplicativo (cliente)</span><span class="sxs-lookup"><span data-stu-id="75a0d-155">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="75a0d-156">Fluxo de usuário de inscrição/entrada</span><span class="sxs-lookup"><span data-stu-id="75a0d-156">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="75a0d-157">Domínio primário/Publicador/locatário</span><span class="sxs-lookup"><span data-stu-id="75a0d-157">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="75a0d-158">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75a0d-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="75a0d-159">No portal do Azure, o **URI de redirecionamento** de configuração de plataforma do aplicativo é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="75a0d-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="75a0d-160">Se o aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="75a0d-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="75a0d-161">Se a porta não foi configurada anteriormente com a porta conhecida do aplicativo, retorne ao registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="75a0d-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="75a0d-162">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="75a0d-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="75a0d-163">Faça logon no aplicativo usando uma conta de usuário do AAD.</span><span class="sxs-lookup"><span data-stu-id="75a0d-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="75a0d-164">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="75a0d-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="75a0d-165">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="75a0d-165">For more information, see:</span></span>
  * [<span data-ttu-id="75a0d-166">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="75a0d-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="75a0d-167">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="75a0d-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="75a0d-168">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="75a0d-168">Authentication package</span></span>

<span data-ttu-id="75a0d-169">Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="75a0d-169">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="75a0d-170">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="75a0d-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="75a0d-171">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="75a0d-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="75a0d-172">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="75a0d-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="75a0d-173">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75a0d-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="75a0d-174">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="75a0d-174">Authentication service support</span></span>

<span data-ttu-id="75a0d-175">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote.</span><span class="sxs-lookup"><span data-stu-id="75a0d-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="75a0d-176">Esse método configura todos os serviços necessários para que o aplicativo interaja com o :::no-loc(Identity)::: provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="75a0d-176">This method sets up all of the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="75a0d-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="75a0d-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="75a0d-178">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75a0d-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="75a0d-179">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75a0d-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="75a0d-180">A configuração é fornecida pelo `wwwroot/:::no-loc(appsettings.json):::` arquivo:</span><span class="sxs-lookup"><span data-stu-id="75a0d-180">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="75a0d-181">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="75a0d-181">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="75a0d-182">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="75a0d-182">Access token scopes</span></span>

<span data-ttu-id="75a0d-183">O :::no-loc(Blazor WebAssembly)::: modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="75a0d-183">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="75a0d-184">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="75a0d-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="75a0d-185">Especifique escopos adicionais com `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="75a0d-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="75a0d-186">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="75a0d-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="75a0d-187">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="75a0d-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="75a0d-188">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="75a0d-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="75a0d-189">Modo de logon</span><span class="sxs-lookup"><span data-stu-id="75a0d-189">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="75a0d-190">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="75a0d-190">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="75a0d-191">Página de índice</span><span class="sxs-lookup"><span data-stu-id="75a0d-191">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="75a0d-192">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="75a0d-192">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="75a0d-193">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="75a0d-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="75a0d-194">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="75a0d-194">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="75a0d-195">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="75a0d-195">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="75a0d-196">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="75a0d-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="75a0d-197">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="75a0d-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="75a0d-198">Tutorial: Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="75a0d-198">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="75a0d-199">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="75a0d-199">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
