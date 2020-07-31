---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core para IIS e HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330672"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="0142e-103">Configurar a autenticação do Windows no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0142e-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="0142e-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0142e-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0142e-105">A autenticação do Windows (também conhecida como autenticação Negotiate, Kerberos ou NTLM) pode ser configurada para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)ou [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0142e-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0142e-106">A autenticação do Windows (também conhecida como autenticação de negociação, Kerberos ou NTLM) pode ser configurada para aplicativos ASP.NET Core hospedados com o [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0142e-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="0142e-107">A autenticação do Windows depende do sistema operacional para autenticar usuários de ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0142e-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="0142e-108">Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando Active Directory identidades de domínio ou contas do Windows para identificar os usuários.</span><span class="sxs-lookup"><span data-stu-id="0142e-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="0142e-109">A autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores Web pertencem ao mesmo domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="0142e-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="0142e-110">Não há suporte para a autenticação do Windows com HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0142e-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="0142e-111">Os desafios de autenticação podem ser enviados em respostas HTTP/2, mas o cliente deve fazer o downgrade para HTTP/1.1 antes de autenticar.</span><span class="sxs-lookup"><span data-stu-id="0142e-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="0142e-112">Cenários de balanceador de carga e proxy</span><span class="sxs-lookup"><span data-stu-id="0142e-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="0142e-113">A autenticação do Windows é um cenário com estado usado principalmente em uma intranet, em que um proxy ou balanceador de carga geralmente não trata o tráfego entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="0142e-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="0142e-114">Se um proxy ou um balanceador de carga for usado, a autenticação do Windows só funcionará se o proxy ou o balanceador de carga:</span><span class="sxs-lookup"><span data-stu-id="0142e-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="0142e-115">Manipula a autenticação.</span><span class="sxs-lookup"><span data-stu-id="0142e-115">Handles the authentication.</span></span>
* <span data-ttu-id="0142e-116">Passa as informações de autenticação do usuário para o aplicativo (por exemplo, em um cabeçalho de solicitação), que atua nas informações de autenticação.</span><span class="sxs-lookup"><span data-stu-id="0142e-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="0142e-117">Uma alternativa à autenticação do Windows em ambientes em que proxies e balanceadores de carga são usados é Active Directory serviços federados (ADFS) com o OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="0142e-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="0142e-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="0142e-118">IIS/IIS Express</span></span>

<span data-ttu-id="0142e-119">Adicione serviços de autenticação invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0142e-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="0142e-120">Configurações de inicialização (depurador)</span><span class="sxs-lookup"><span data-stu-id="0142e-120">Launch settings (debugger)</span></span>

<span data-ttu-id="0142e-121">A configuração das configurações de inicialização afeta apenas as *Propriedades/launchSettings.jsno* arquivo para IIS Express e não configura o IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0142e-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="0142e-122">A configuração do servidor é explicada na seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="0142e-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="0142e-123">O modelo de **aplicativo Web** disponível por meio do Visual Studio ou do CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows, que atualiza as *Propriedades/launchSettings.jsautomaticamente no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0142e-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0142e-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0142e-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0142e-125">**Novo Projeto**</span><span class="sxs-lookup"><span data-stu-id="0142e-125">**New project**</span></span>

1. <span data-ttu-id="0142e-126">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="0142e-126">Create a new project.</span></span>
1. <span data-ttu-id="0142e-127">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0142e-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="0142e-128">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0142e-128">Select **Next**.</span></span>
1. <span data-ttu-id="0142e-129">Forneça um nome no campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="0142e-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="0142e-130">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="0142e-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0142e-131">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0142e-131">Select **Create**.</span></span>
1. <span data-ttu-id="0142e-132">Selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="0142e-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="0142e-133">Na janela **alterar autenticação** , selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="0142e-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="0142e-134">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="0142e-134">Select **OK**.</span></span>
1. <span data-ttu-id="0142e-135">Selecione **aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="0142e-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="0142e-136">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0142e-136">Select **Create**.</span></span>

<span data-ttu-id="0142e-137">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0142e-137">Run the app.</span></span> <span data-ttu-id="0142e-138">O nome de usuário aparece na interface do usuário do aplicativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="0142e-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="0142e-139">**Projeto existente**</span><span class="sxs-lookup"><span data-stu-id="0142e-139">**Existing project**</span></span>

<span data-ttu-id="0142e-140">As propriedades do projeto habilitam a autenticação do Windows e desabilitam a autenticação anônima:</span><span class="sxs-lookup"><span data-stu-id="0142e-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="0142e-141">Clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="0142e-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="0142e-142">Selecione a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="0142e-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="0142e-143">Desmarque a caixa de seleção **habilitar autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="0142e-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="0142e-144">Marque a caixa de seleção para **habilitar a autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="0142e-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="0142e-145">Salve e feche a página de propriedades.</span><span class="sxs-lookup"><span data-stu-id="0142e-145">Save and close the property page.</span></span>

<span data-ttu-id="0142e-146">Como alternativa, as propriedades podem ser configuradas no `iisSettings` nó do *launchSettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="0142e-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0142e-147">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0142e-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0142e-148">**Novo Projeto**</span><span class="sxs-lookup"><span data-stu-id="0142e-148">**New project**</span></span>

<span data-ttu-id="0142e-149">Execute o comando [dotnet New](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (ASP.NET Core aplicativo Web) e o `--auth Windows` comutador:</span><span class="sxs-lookup"><span data-stu-id="0142e-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="0142e-150">**Projeto existente**</span><span class="sxs-lookup"><span data-stu-id="0142e-150">**Existing project**</span></span>

<span data-ttu-id="0142e-151">Atualize o `iisSettings` nó do *launchSettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="0142e-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="0142e-152">Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) **ou** o pacote NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="0142e-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="0142e-153">IIS</span><span class="sxs-lookup"><span data-stu-id="0142e-153">IIS</span></span>

<span data-ttu-id="0142e-154">O IIS usa o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0142e-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="0142e-155">A autenticação do Windows é configurada para o IIS por meio do arquivo *web.config* .</span><span class="sxs-lookup"><span data-stu-id="0142e-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="0142e-156">As seções a seguir mostram como:</span><span class="sxs-lookup"><span data-stu-id="0142e-156">The following sections show how to:</span></span>

* <span data-ttu-id="0142e-157">Forneça um arquivo de *web.config* local que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.</span><span class="sxs-lookup"><span data-stu-id="0142e-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="0142e-158">Use o Gerenciador do IIS para configurar o arquivo de *web.config* de um aplicativo ASP.NET Core que já foi implantado no servidor.</span><span class="sxs-lookup"><span data-stu-id="0142e-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="0142e-159">Se você ainda não tiver feito isso, habilite o IIS para hospedar ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0142e-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="0142e-160">Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="0142e-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="0142e-161">Habilite o serviço de função do IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0142e-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="0142e-162">Para obter mais informações, consulte [habilitar a autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="0142e-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="0142e-163">O [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) é configurado para autenticar automaticamente as solicitações por padrão.</span><span class="sxs-lookup"><span data-stu-id="0142e-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="0142e-164">Para obter mais informações, consulte [Host ASP.NET Core no Windows com IIS: opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="0142e-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="0142e-165">O módulo ASP.NET Core é configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="0142e-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="0142e-166">Para obter mais informações, consulte [referência de configuração de módulo ASP.NET Core: atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="0142e-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="0142e-167">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="0142e-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="0142e-168">**Antes de publicar e implantar o projeto,** adicione o seguinte arquivo de *web.config* à raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="0142e-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="0142e-169">Quando o projeto é publicado pelo SDK do .NET Core (sem a `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), o arquivo de *web.config* publicado inclui a `<location><system.webServer><security><authentication>` seção.</span><span class="sxs-lookup"><span data-stu-id="0142e-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="0142e-170">Para obter mais informações sobre a `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="0142e-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="0142e-171">**Depois de publicar e implantar o projeto,** execute a configuração do lado do servidor com o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="0142e-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="0142e-172">No Gerenciador do IIS, selecione o site do IIS no nó **sites** da barra lateral **conexões** .</span><span class="sxs-lookup"><span data-stu-id="0142e-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="0142e-173">Clique duas vezes em **autenticação** na área do **IIS** .</span><span class="sxs-lookup"><span data-stu-id="0142e-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="0142e-174">Selecione **autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="0142e-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="0142e-175">Selecione **desabilitar** na barra lateral **ações** .</span><span class="sxs-lookup"><span data-stu-id="0142e-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="0142e-176">Selecione **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="0142e-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="0142e-177">Selecione **habilitar** na barra lateral **ações** .</span><span class="sxs-lookup"><span data-stu-id="0142e-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="0142e-178">Quando essas ações são realizadas, o Gerenciador do IIS modifica o arquivo de *web.config* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0142e-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="0142e-179">Um `<system.webServer><security><authentication>` nó é adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="0142e-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="0142e-180">A `<system.webServer>` seção adicionada ao arquivo de *web.config* pelo Gerenciador do IIS está fora da seção do aplicativo `<location>` adicionada pelo SDK do .NET Core quando o aplicativo é publicado.</span><span class="sxs-lookup"><span data-stu-id="0142e-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="0142e-181">Como a seção é adicionada fora do `<location>` nó, as configurações são herdadas por qualquer [subaplicativo](xref:host-and-deploy/iis/index#sub-applications) para o aplicativo atual.</span><span class="sxs-lookup"><span data-stu-id="0142e-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="0142e-182">Para impedir a herança, mova a `<security>` seção adicionada dentro da `<location><system.webServer>` seção que o SDK do .NET Core fornecido.</span><span class="sxs-lookup"><span data-stu-id="0142e-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="0142e-183">Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta apenas o arquivo de *web.config* do aplicativo no servidor.</span><span class="sxs-lookup"><span data-stu-id="0142e-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="0142e-184">Uma implantação subsequente do aplicativo poderá substituir as configurações no servidor se a cópia do servidor de *web.config* for substituída pelo arquivo de *web.config* do projeto.</span><span class="sxs-lookup"><span data-stu-id="0142e-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="0142e-185">Use **uma** das seguintes abordagens para gerenciar as configurações:</span><span class="sxs-lookup"><span data-stu-id="0142e-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="0142e-186">Use o Gerenciador do IIS para redefinir as configurações no arquivo de *web.config* depois que o arquivo for substituído na implantação.</span><span class="sxs-lookup"><span data-stu-id="0142e-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="0142e-187">Adicione um *arquivo deweb.config* ao aplicativo localmente com as configurações.</span><span class="sxs-lookup"><span data-stu-id="0142e-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="0142e-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0142e-188">Kestrel</span></span>

<span data-ttu-id="0142e-189">O pacote NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) pode ser usado com o [Kestrel](xref:fundamentals/servers/kestrel) para dar suporte à autenticação do Windows usando Negotiate e Kerberos no Windows, Linux e MacOS.</span><span class="sxs-lookup"><span data-stu-id="0142e-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="0142e-190">As credenciais podem ser mantidas entre solicitações em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="0142e-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="0142e-191">*A autenticação de negociação não deve ser usada com proxies, a menos que o proxy mantenha uma afinidade de conexão 1:1 (uma conexão persistente) com Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="0142e-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="0142e-192">O manipulador Negotiate detecta se o servidor subjacente dá suporte à autenticação do Windows nativamente e se está habilitado.</span><span class="sxs-lookup"><span data-stu-id="0142e-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="0142e-193">Se o servidor oferecer suporte à autenticação do Windows, mas estiver desabilitado, um erro será gerado solicitando que você habilite a implementação do servidor.</span><span class="sxs-lookup"><span data-stu-id="0142e-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="0142e-194">Quando a autenticação do Windows está habilitada no servidor, o manipulador Negotiate encaminha-se de forma transparente para ele.</span><span class="sxs-lookup"><span data-stu-id="0142e-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="0142e-195">Adicione serviços de autenticação invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0142e-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="0142e-196">Adicione o middleware de autenticação chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0142e-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="0142e-197">Para obter mais informações sobre o middleware, consulte <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="0142e-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="0142e-198">Solicitações anônimas são permitidas.</span><span class="sxs-lookup"><span data-stu-id="0142e-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="0142e-199">Use [ASP.NET Core autorização](xref:security/authorization/introduction) para desafiar solicitações anônimas de autenticação.</span><span class="sxs-lookup"><span data-stu-id="0142e-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="0142e-200">Configuração de ambiente do Windows</span><span class="sxs-lookup"><span data-stu-id="0142e-200">Windows environment configuration</span></span>

<span data-ttu-id="0142e-201">O componente [Microsoft. AspNetCore. Authentication. Negotiation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) executa a autenticação de modo de usuário.</span><span class="sxs-lookup"><span data-stu-id="0142e-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="0142e-202">Os SPNs (nomes da entidade de serviço) devem ser adicionados à conta de usuário que executa o serviço, não à conta da máquina.</span><span class="sxs-lookup"><span data-stu-id="0142e-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="0142e-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` em um shell de comando administrativo.</span><span class="sxs-lookup"><span data-stu-id="0142e-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="0142e-204">Configuração de ambiente Linux e macOS</span><span class="sxs-lookup"><span data-stu-id="0142e-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="0142e-205">As instruções para ingressar em um computador Linux ou macOS em um domínio do Windows estão disponíveis no artigo [conectar Azure Data Studio ao seu SQL Server usando a autenticação do Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="0142e-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="0142e-206">As instruções criam uma conta de computador para o computador Linux no domínio.</span><span class="sxs-lookup"><span data-stu-id="0142e-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="0142e-207">Os SPNs devem ser adicionados a essa conta de computador.</span><span class="sxs-lookup"><span data-stu-id="0142e-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="0142e-208">Ao seguir as diretrizes no artigo [conectar Azure Data Studio ao seu SQL Server usando a autenticação do Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , substitua `python-software-properties` por `python3-software-properties` se necessário.</span><span class="sxs-lookup"><span data-stu-id="0142e-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="0142e-209">Depois que o computador Linux ou macOS for ingressado no domínio, serão necessárias etapas adicionais para fornecer um [arquivo keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) com os SPNs:</span><span class="sxs-lookup"><span data-stu-id="0142e-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="0142e-210">No controlador de domínio, adicione novos SPNs de serviço Web à conta do computador:</span><span class="sxs-lookup"><span data-stu-id="0142e-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="0142e-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) para gerar um arquivo keytab:</span><span class="sxs-lookup"><span data-stu-id="0142e-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="0142e-212">Alguns campos devem ser especificados em letras maiúsculas, conforme indicado.</span><span class="sxs-lookup"><span data-stu-id="0142e-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="0142e-213">Copie o arquivo keytab para o computador Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="0142e-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="0142e-214">Selecione o arquivo keytab por meio de uma variável de ambiente:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="0142e-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="0142e-215">Invocar `klist` para mostrar os SPNs disponíveis no momento para uso.</span><span class="sxs-lookup"><span data-stu-id="0142e-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="0142e-216">Um arquivo keytab contém credenciais de acesso ao domínio e deve ser protegido de acordo.</span><span class="sxs-lookup"><span data-stu-id="0142e-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="0142e-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0142e-217">HTTP.sys</span></span>

<span data-ttu-id="0142e-218">O [HTTP.sys](xref:fundamentals/servers/httpsys) dá suporte à autenticação do Windows no modo kernel usando a autenticação Negotiate, NTLM ou básica.</span><span class="sxs-lookup"><span data-stu-id="0142e-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="0142e-219">Adicione serviços de autenticação invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0142e-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="0142e-220">Configure o host da Web do aplicativo para usar HTTP.sys com a autenticação do Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="0142e-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="0142e-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>está no <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="0142e-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0142e-222">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="0142e-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="0142e-223">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0142e-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="0142e-224">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="0142e-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="0142e-225">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0142e-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0142e-226">Não há suporte para HTTP.sys no nano Server versão 1709 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="0142e-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="0142e-227">Para usar a autenticação do Windows e HTTP.sys com o nano Server, use um [contêiner do Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="0142e-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="0142e-228">Para obter mais informações sobre o Server Core, consulte [o que é a opção de instalação do Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="0142e-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="0142e-229">Autorizar usuários</span><span class="sxs-lookup"><span data-stu-id="0142e-229">Authorize users</span></span>

<span data-ttu-id="0142e-230">O estado de configuração do acesso anônimo determina a maneira como os `[Authorize]` `[AllowAnonymous]` atributos e são usados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0142e-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="0142e-231">As duas seções a seguir explicam como lidar com os Estados de configuração não permitido e permitidos de acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="0142e-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="0142e-232">Não permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="0142e-232">Disallow anonymous access</span></span>

<span data-ttu-id="0142e-233">Quando a autenticação do Windows estiver habilitada e o acesso anônimo estiver desabilitado, os `[Authorize]` `[AllowAnonymous]` atributos e não terão nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="0142e-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="0142e-234">Se um site do IIS estiver configurado para não permitir acesso anônimo, a solicitação nunca atingirá o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0142e-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="0142e-235">Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.</span><span class="sxs-lookup"><span data-stu-id="0142e-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="0142e-236">Permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="0142e-236">Allow anonymous access</span></span>

<span data-ttu-id="0142e-237">Quando a autenticação do Windows e o acesso anônimo estiverem habilitados, use os `[Authorize]` `[AllowAnonymous]` atributos e.</span><span class="sxs-lookup"><span data-stu-id="0142e-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="0142e-238">O `[Authorize]` atributo permite que você proteja pontos de extremidade do aplicativo que exigem autenticação.</span><span class="sxs-lookup"><span data-stu-id="0142e-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="0142e-239">O `[AllowAnonymous]` atributo substitui o `[Authorize]` atributo em aplicativos que permitem acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="0142e-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="0142e-240">Para obter detalhes de uso do atributo, consulte <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="0142e-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="0142e-241">Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia.</span><span class="sxs-lookup"><span data-stu-id="0142e-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="0142e-242">O [middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) pode ser configurado para fornecer aos usuários uma experiência de "acesso negado" melhor.</span><span class="sxs-lookup"><span data-stu-id="0142e-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="0142e-243">Representação</span><span class="sxs-lookup"><span data-stu-id="0142e-243">Impersonation</span></span>

<span data-ttu-id="0142e-244">ASP.NET Core não implementa a representação.</span><span class="sxs-lookup"><span data-stu-id="0142e-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="0142e-245">Os aplicativos são executados com a identidade do aplicativo para todas as solicitações, usando o pool de aplicativos ou a identidade do processo.</span><span class="sxs-lookup"><span data-stu-id="0142e-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="0142e-246">Se o aplicativo deve executar uma ação em nome de um usuário, use [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido de terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0142e-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="0142e-247">Execute uma única ação neste contexto e, em seguida, feche o contexto.</span><span class="sxs-lookup"><span data-stu-id="0142e-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="0142e-248">`RunImpersonated`o não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos.</span><span class="sxs-lookup"><span data-stu-id="0142e-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="0142e-249">Por exemplo, o encapsulamento de solicitações inteiras ou cadeias de middleware não tem suporte ou é recomendado.</span><span class="sxs-lookup"><span data-stu-id="0142e-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0142e-250">Embora o pacote [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) habilite a autenticação no Windows, no Linux e no MacOS, há suporte para a representação apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="0142e-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="0142e-251">Transformações de declarações</span><span class="sxs-lookup"><span data-stu-id="0142e-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0142e-252">Ao hospedar com o IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="0142e-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="0142e-253">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="0142e-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="0142e-254">Para obter mais informações e um exemplo de código que ativa as transformações de declarações, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="0142e-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0142e-255">Ao hospedar com o IIS no modo de processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> o não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="0142e-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="0142e-256">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="0142e-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="0142e-257">Para obter mais informações e um exemplo de código que ativa transformações de declarações ao hospedar em processo, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="0142e-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0142e-258">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0142e-258">Additional resources</span></span>

* [<span data-ttu-id="0142e-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="0142e-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
