---
title: 'Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core autônomo com Azure Active Directory'
author: guardrex
description: 'Saiba como proteger um aplicativo ASP.NET Core :::no-loc(Blazor WebAssembly)::: autônomo com Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 46e5a422864dd8f6aef72afddb3b406bc99f9163
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690422"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="95897-103">Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core autônomo com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="95897-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory</span></span>

<span data-ttu-id="95897-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="95897-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="95897-105">Este artigo aborda como proteger um aplicativo ASP.NET Core :::no-loc(Blazor WebAssembly)::: autônomo com Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="95897-105">This article covers how to secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory (AAD).</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="95897-106">Para :::no-loc(Blazor WebAssembly)::: aplicativos criados no Visual Studio que são configurados para dar suporte a contas em um diretório organizacional do AAD, o Visual Studio não configura o aplicativo corretamente na geração do projeto.</span><span class="sxs-lookup"><span data-stu-id="95897-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="95897-107">Isso será abordado em uma versão futura do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="95897-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="95897-108">Este artigo mostra como criar o aplicativo com o comando do CLI do .NET Core `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="95897-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="95897-109">Se você preferir criar o aplicativo com o Visual Studio antes de o IDE ser atualizado para os :::no-loc(Blazor)::: modelos mais recentes no ASP.NET Core 5,0, consulte cada seção deste artigo e confirme ou atualize a configuração do aplicativo após o Visual Studio criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

<span data-ttu-id="95897-110">Para criar um [ :::no-loc(Blazor WebAssembly)::: aplicativo autônomo](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="95897-110">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="95897-111">[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="95897-111">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="95897-112">Registre um aplicativo do AAD na área **Azure Active Directory**  >  **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="95897-112">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="95897-113">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor)::: AAD autônomo** ).</span><span class="sxs-lookup"><span data-stu-id="95897-113">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD** ).</span></span>
1. <span data-ttu-id="95897-114">Escolha um **tipo de conta com suporte** .</span><span class="sxs-lookup"><span data-stu-id="95897-114">Choose a **Supported account types** .</span></span> <span data-ttu-id="95897-115">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="95897-115">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="95897-116">Defina a lista suspensa **URI de redirecionamento** para o **aplicativo de página única (Spa)** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="95897-116">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="95897-117">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="95897-117">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="95897-118">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-118">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="95897-119">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="95897-119">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="95897-120">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="95897-120">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="95897-121">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="95897-121">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="95897-122">Desmarque a caixa de seleção **permissões** > **conceder consentimento de administrador às permissões OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="95897-122">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="95897-123">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="95897-123">Select **Register** .</span></span>

<span data-ttu-id="95897-124">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="95897-124">Record the following information:</span></span>

* <span data-ttu-id="95897-125">ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="95897-125">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="95897-126">ID do diretório (locatário) (por exemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="95897-126">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="95897-127">Em configurações de plataforma de **autenticação** > **Platform configurations** > **, um aplicativo de página única (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="95897-127">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="95897-128">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="95897-128">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="95897-129">Para **concessão implícita** , verifique se as caixas de seleção para **tokens de acesso** e **tokens de ID** **não** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="95897-129">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="95897-130">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="95897-130">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="95897-131">Selecione o botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="95897-131">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="95897-132">Forneça um **nome** para o aplicativo (por exemplo, **:::no-loc(Blazor)::: AAD autônomo** ).</span><span class="sxs-lookup"><span data-stu-id="95897-132">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD** ).</span></span>
1. <span data-ttu-id="95897-133">Escolha um **tipo de conta com suporte** .</span><span class="sxs-lookup"><span data-stu-id="95897-133">Choose a **Supported account types** .</span></span> <span data-ttu-id="95897-134">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="95897-134">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="95897-135">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="95897-135">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="95897-136">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="95897-136">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="95897-137">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-137">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="95897-138">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="95897-138">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="95897-139">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="95897-139">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="95897-140">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="95897-140">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="95897-141">Desmarque a caixa de seleção **permissões** > **conceder consentimento de administrador às permissões OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="95897-141">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="95897-142">Selecione **Registrar** .</span><span class="sxs-lookup"><span data-stu-id="95897-142">Select **Register** .</span></span>

<span data-ttu-id="95897-143">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="95897-143">Record the following information:</span></span>

* <span data-ttu-id="95897-144">ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="95897-144">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="95897-145">ID do diretório (locatário) (por exemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="95897-145">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="95897-146">Em **Authentication** > **configurações da plataforma** de autenticação > **Web** :</span><span class="sxs-lookup"><span data-stu-id="95897-146">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="95897-147">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="95897-147">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="95897-148">Para **concessão implícita** , marque as caixas de seleção para **tokens de acesso** e **tokens de ID** .</span><span class="sxs-lookup"><span data-stu-id="95897-148">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="95897-149">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="95897-149">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="95897-150">Selecione o botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="95897-150">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="95897-151">Crie o aplicativo em uma pasta vazia.</span><span class="sxs-lookup"><span data-stu-id="95897-151">Create the app in an empty folder.</span></span> <span data-ttu-id="95897-152">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="95897-152">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="95897-153">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="95897-153">Placeholder</span></span>   | <span data-ttu-id="95897-154">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="95897-154">Azure portal name</span></span>       | <span data-ttu-id="95897-155">Exemplo</span><span class="sxs-lookup"><span data-stu-id="95897-155">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `:::no-loc(Blazor):::Sample`                         |
| `{CLIENT ID}` | <span data-ttu-id="95897-156">ID do aplicativo (cliente)</span><span class="sxs-lookup"><span data-stu-id="95897-156">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="95897-157">ID do diretório (locatário)</span><span class="sxs-lookup"><span data-stu-id="95897-157">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="95897-158">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="95897-159">No portal do Azure, o **URI de redirecionamento** de configuração de plataforma do aplicativo é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="95897-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="95897-160">Se o aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="95897-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="95897-161">Se a porta não foi configurada anteriormente com a porta conhecida do aplicativo, retorne ao registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="95897-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="95897-162">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="95897-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="95897-163">Faça logon no aplicativo usando uma conta de usuário do AAD.</span><span class="sxs-lookup"><span data-stu-id="95897-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="95897-164">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="95897-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="95897-165">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="95897-165">For more information, see:</span></span>
  * [<span data-ttu-id="95897-166">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="95897-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="95897-167">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="95897-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="95897-168">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="95897-168">Authentication package</span></span>

<span data-ttu-id="95897-169">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="95897-169">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="95897-170">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="95897-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="95897-171">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="95897-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="95897-172">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="95897-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="95897-173">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="95897-174">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="95897-174">Authentication service support</span></span>

<span data-ttu-id="95897-175">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote.</span><span class="sxs-lookup"><span data-stu-id="95897-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="95897-176">Esse método configura os serviços necessários para que o aplicativo interaja com o :::no-loc(Identity)::: provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="95897-176">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="95897-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="95897-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="95897-178">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="95897-179">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95897-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="95897-180">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="95897-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="95897-181">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="95897-181">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="95897-182">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="95897-182">Access token scopes</span></span>

<span data-ttu-id="95897-183">O :::no-loc(Blazor WebAssembly)::: modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="95897-183">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="95897-184">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="95897-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="95897-185">Especifique escopos adicionais com `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="95897-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="95897-186">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="95897-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="95897-187">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="95897-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="95897-188">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="95897-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="95897-189">Modo de logon</span><span class="sxs-lookup"><span data-stu-id="95897-189">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="95897-190">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="95897-190">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="95897-191">Página de índice</span><span class="sxs-lookup"><span data-stu-id="95897-191">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="95897-192">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="95897-192">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="95897-193">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="95897-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="95897-194">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="95897-194">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="95897-195">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="95897-195">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="95897-196">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95897-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="95897-197">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="95897-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="95897-198">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="95897-198">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
