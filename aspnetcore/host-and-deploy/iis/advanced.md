---
title: Configuração avançada
author: rick-anderson
description: Configuração avançada com o módulo ASP.NET Core e o Serviços de Informações da Internet (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: ad2480faeea2f07e51585f5bc6a1c63b3a0b1668
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755227"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="6b5e0-103">Configuração avançada do módulo ASP.NET Core e do IIS</span><span class="sxs-lookup"><span data-stu-id="6b5e0-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="6b5e0-104">Este artigo aborda opções de configuração avançadas e cenários para o módulo ASP.NET Core e o IIS.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="6b5e0-105">Modificar o tamanho da pilha</span><span class="sxs-lookup"><span data-stu-id="6b5e0-105">Modify the stack size</span></span>

<span data-ttu-id="6b5e0-106">*Aplica-se somente ao usar o modelo de hospedagem em processo.*</span><span class="sxs-lookup"><span data-stu-id="6b5e0-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="6b5e0-107">Configure o tamanho da pilha gerenciada usando a `stackSize` configuração em bytes no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="6b5e0-108">O tamanho padrão é 1.048.576 bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="6b5e0-109">O exemplo a seguir altera o tamanho da pilha para 2 MB (2.097.152 bytes):</span><span class="sxs-lookup"><span data-stu-id="6b5e0-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="6b5e0-110">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="6b5e0-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="6b5e0-111">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="6b5e0-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="6b5e0-112">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="6b5e0-113">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="6b5e0-114">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="6b5e0-115">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="6b5e0-116">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="6b5e0-117">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="6b5e0-118">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="6b5e0-119">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="6b5e0-120">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="6b5e0-121">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="6b5e0-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="6b5e0-122">O instalador do módulo ASP.NET Core é executado com os privilégios da `TrustedInstaller` conta.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="6b5e0-123">Como a conta do sistema local não tem permissão Modificar para o caminho de compartilhamento usado pela configuração compartilhada do IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações do módulo no `applicationHost.config` arquivo no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="6b5e0-124">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="6b5e0-125">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="6b5e0-126">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="6b5e0-127">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-127">Run the installer.</span></span>
1. <span data-ttu-id="6b5e0-128">Exporte o `applicationHost.config` arquivo atualizado para o compartilhamento de arquivos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="6b5e0-129">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="6b5e0-130">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="6b5e0-130">Data protection</span></span>

<span data-ttu-id="6b5e0-131">A [pilha Proteção de Dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) ASP.NET Core, incluindo aqueles usados na autenticação.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="6b5e0-132">Mesmo se as APIs de proteção de dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada com um script de implantação ou no código do usuário para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="6b5e0-133">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="6b5e0-134">Se o anel da chave de proteção de dados for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="6b5e0-135">Os cookie tokens de autenticação baseados em todos os são invalidados.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="6b5e0-136">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="6b5e0-137">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="6b5e0-138">Isso pode incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core cookie s de TempData do MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="6b5e0-139">Para configurar a proteção de dados no IIS para persistir o token de autenticação, use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="6b5e0-140">**Criar chaves do registro de proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="6b5e0-141">As chaves de proteção de dados usadas por ASP.NET Core aplicativos são armazenadas no registro externo para os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="6b5e0-142">Para manter as chaves de um determinado aplicativo, crie chaves do registro para o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="6b5e0-143">Para instalações autônomas do IIS não Web Farm, você pode usar o [script Provision-AutoGenKeys.ps1 de Proteção de Dados do PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada pool de aplicativos usado com um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="6b5e0-144">Esse script cria uma chave do registro no registro HKLM que é acessível somente para a conta de processo de trabalho do pool de aplicativos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="6b5e0-145">As chaves são criptografadas em repouso usando a DPAPI com uma chave para todo o computador.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="6b5e0-146">Em cenários de web farm, um aplicativo pode ser configurado para usar um caminho UNC para armazenar seu anel de chave de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="6b5e0-147">Por padrão, as chaves não são criptografadas.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="6b5e0-148">Verifique se as permissões de arquivo para o compartilhamento de rede estão limitadas à conta do Windows em que o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="6b5e0-149">Um certificado X509 pode ser usado para proteger chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="6b5e0-150">Considere um mecanismo para permitir que os usuários carreguem certificados.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="6b5e0-151">coloque os certificados no repositório de certificados confiáveis do usuário e certifique-se de que eles estejam disponíveis em todos os computadores nos quais o aplicativo do usuário é executado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="6b5e0-152">Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="6b5e0-153">**Configurar o pool de aplicativos do IIS para carregar o perfil do usuário**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="6b5e0-154">Essa configuração está na seção **Modelo de processo** nas **Configurações avançadas** do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="6b5e0-155">Defina **carregar perfil do usuário** como `True` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="6b5e0-156">Quando definido como `True`, as chaves são armazenadas no diretório do perfil do usuário e protegidas usando DPAPI com uma chave específica para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="6b5e0-157">As chaves são mantidas na `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` pasta.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="6b5e0-158">O [ `setProfileEnvironment` atributo](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="6b5e0-159">O valor padrão de `setProfileEnvironment` é `true`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="6b5e0-160">Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="6b5e0-161">Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="6b5e0-162">Navegue até a pasta `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="6b5e0-163">Abra o arquivo `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="6b5e0-164">Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="6b5e0-165">Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="6b5e0-166">**Usar o sistema de arquivos como um repositório de tokens de autenticação**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="6b5e0-167">Ajuste o código do aplicativo para [usar o sistema de arquivos como um repositório de tokens de autenticação](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="6b5e0-168">Use um certificado X509 para proteger o token de autenticação e verifique se ele é um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="6b5e0-169">Se o certificado for autoassinado, você deverá colocá-lo no repositório Raiz confiável.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="6b5e0-170">Ao usar o IIS em uma web farm:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="6b5e0-171">Use um compartilhamento de arquivos que pode ser acessado por todos os computadores.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="6b5e0-172">Implante um certificado X509 em cada computador.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="6b5e0-173">Configure a [proteção de dados no código](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="6b5e0-174">**Definir uma política de todo o computador para proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="6b5e0-175">O sistema de proteção de dados tem suporte limitado para definir uma [política padrão em todo o computador](xref:security/data-protection/configuration/machine-wide-policy) para todos os aplicativos que consomem as APIs de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="6b5e0-176">Para obter mais informações, consulte <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="6b5e0-177">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="6b5e0-177">IIS configuration</span></span>

<span data-ttu-id="6b5e0-178">**Sistemas operacionais do Windows Server**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="6b5e0-179">Habilite a função **Servidor Web (IIS)** e estabeleça serviços de função.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="6b5e0-180">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="6b5e0-181">Na etapa **Funções de Servidor**, marque a caixa de **Servidor Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![A função de Servidor Web IIS é selecionada na etapa Selecionar funções de servidor.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="6b5e0-183">Após a etapa **Recursos**, a etapa **Serviços de função** é carregada para o servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="6b5e0-184">Selecione os serviços de função do IIS desejados ou aceite os serviços de função padrão fornecidos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Os serviços de função padrão são selecionados na etapa Selecionar serviços de função.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="6b5e0-186">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6b5e0-187">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança do **servidor Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="6b5e0-188">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6b5e0-189">Para obter mais informações, consulte [autenticação `<windowsAuthentication>` do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6b5e0-190">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6b5e0-191">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6b5e0-192">Para habilitar WebSockets, expanda os seguintes nós: desenvolvimento de aplicativo de **servidor Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="6b5e0-193">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6b5e0-194">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6b5e0-195">Continue para a etapa **Confirmação** para instalar os serviços e a função de servidor Web.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="6b5e0-196">Uma reinicialização de servidor/IIS não é necessária após a instalação da função do **servidor Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="6b5e0-197">**Sistemas operacionais de área de trabalho do Windows**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="6b5e0-198">Habilite o **Console de Gerenciamento do IIS** e os **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6b5e0-199">Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="6b5e0-200">Abra o nó **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="6b5e0-201">Abra o nó **Ferramentas de Gerenciamento da Web**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="6b5e0-202">Marque a caixa de **Console de Gerenciamento do IIS**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="6b5e0-203">Marque a caixa de **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6b5e0-204">Aceite os recursos padrão dos **Serviços na World Wide Web** ou personalize os recursos do IIS.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="6b5e0-205">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6b5e0-206">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança **dos serviços de World Wide Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="6b5e0-207">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6b5e0-208">Para obter mais informações, consulte [autenticação `<windowsAuthentication>` do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6b5e0-209">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6b5e0-210">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6b5e0-211">Para habilitar WebSockets, expanda os seguintes nós: recursos de desenvolvimento de aplicativos **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="6b5e0-212">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6b5e0-213">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6b5e0-214">Se a instalação do IIS exigir uma reinicialização, reinicie o sistema.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-214">If the IIS installation requires a restart, restart the system.</span></span>

![O Console de Gerenciamento do IIS e os Serviços na World Wide Web estão selecionados em Recursos do Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="6b5e0-216">Diretórios virtuais</span><span class="sxs-lookup"><span data-stu-id="6b5e0-216">Virtual Directories</span></span>

<span data-ttu-id="6b5e0-217">[Diretórios virtuais IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) não são compatíveis com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="6b5e0-218">Um aplicativo pode ser hospedado como um [subaplicativo](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="6b5e0-219">Subaplicativos</span><span class="sxs-lookup"><span data-stu-id="6b5e0-219">Sub-applications</span></span>

<span data-ttu-id="6b5e0-220">Um aplicativo ASP.NET Core pode ser hospedado como um [subaplicativo IIS (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="6b5e0-221">A parte do caminho do subaplicativo se torna parte da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="6b5e0-222">Links de ativos estáticos dentro do subaplicativo devem usar a notação de sinal de til e barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="6b5e0-223">A notação de sinal de til e barra aciona um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro) para preceder a base de caminho do subaplicativo ao link relativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="6b5e0-224">Para um subaplicativo no `/subapp_path`, uma imagem vinculada com `src="~/image.png"` é renderizada como `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="6b5e0-225">O Middleware de Arquivo Estático do aplicativo raiz não processa a solicitação de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="6b5e0-226">A solicitação é processada pelo Middleware de Arquivo Estático do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="6b5e0-227">Se um atributo de ativo estático `src` for definido como um caminho absoluto (por exemplo, `src="/image.png"`), o link será renderizado sem a base de caminho do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="6b5e0-228">O Middleware de Arquivos Estáticos do aplicativo raiz tenta fornecer o ativo do [webroot](xref:fundamentals/index#web-root) da raiz do aplicativo, que resulta em uma resposta *404 – Não encontrado*, a menos que o ativo estático esteja disponível no aplicativo raiz.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="6b5e0-229">Para hospedar um aplicativo ASP.NET Core como um subaplicativo em outro aplicativo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="6b5e0-230">Estabeleça um pool de aplicativos para o subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="6b5e0-231">Defina a **versão do CLR do .NET** como **Sem Código Gerenciado** porque o Core Common Language Runtime (CoreCLR) do .NET Core é inicializado para hospedar o aplicativo no processo de trabalho, não no CLR de Área de trabalho (CLR do .NET).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="6b5e0-232">Adicione o site raiz no Gerenciador do IIS com o subaplicativo em uma pasta no site raiz.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="6b5e0-233">Clique com o botão direito do mouse na pasta do subaplicativo no Gerenciador do IIS e selecione **Converter em aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="6b5e0-234">Na caixa de diálogo **Adicionar Aplicativo**, use o botão **Selecionar** no **Pool de Aplicativos** para atribuir o pool de aplicativos que você criou ao subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="6b5e0-235">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-235">Select **OK**.</span></span>

<span data-ttu-id="6b5e0-236">A atribuição de um pool de aplicativos separado para o subaplicativo é um requisito ao usar o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="6b5e0-237">Para obter mais informações sobre o modelo de hospedagem em processo e como configurar o módulo ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="6b5e0-238">Pools de aplicativos</span><span class="sxs-lookup"><span data-stu-id="6b5e0-238">Application Pools</span></span>

<span data-ttu-id="6b5e0-239">O isolamento do pool de aplicativos é determinado pelo modelo de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="6b5e0-240">Hospedagem em processo: os aplicativos devem ser executados em pools de aplicativos separados.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="6b5e0-241">Hospedagem fora do processo: é recomendável isolar os aplicativos uns dos outros executando cada aplicativo em seu próprio pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="6b5e0-242">A caixa de diálogo **Adicionar Site** do IIS usa como padrão um único pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="6b5e0-243">Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="6b5e0-244">Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="6b5e0-245">Pool de aplicativos Identity</span><span class="sxs-lookup"><span data-stu-id="6b5e0-245">Application Pool Identity</span></span>

<span data-ttu-id="6b5e0-246">Uma conta de identidade do pool de aplicativos permite executar um aplicativo em uma conta exclusiva sem a necessidade de criar e gerenciar domínios ou contas locais.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="6b5e0-247">No IIS 8.0 ou posterior, o WAS (Processo de trabalho do administrador) do IIS cria uma conta virtual com o nome do novo pool de aplicativos e executa os processos de trabalho do pool de aplicativos nesta conta por padrão.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="6b5e0-248">No console de gerenciamento do IIS, em **Configurações avançadas** para o pool de aplicativos, verifique se o **Identity** está configurado para usar `ApplicationPoolIdentity` :</span><span class="sxs-lookup"><span data-stu-id="6b5e0-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Caixa de diálogo Configurações avançadas do pool de aplicativos](index/_static/apppool-identity.png)

<span data-ttu-id="6b5e0-250">O processo de gerenciamento do IIS cria um identificador seguro com o nome do pool de aplicativos no Sistema de segurança do Windows.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="6b5e0-251">Os recursos podem ser protegidos usando essa identidade.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="6b5e0-252">No entanto, essa identidade não é uma conta de usuário real e não será mostrada no Console de Gerenciamento de Usuários do Windows.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="6b5e0-253">Se o processo de trabalho do IIS requerer acesso elevado ao aplicativo, modifique a ACL (lista de controle de acesso) do diretório que contém o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="6b5e0-254">Abra o Windows Explorer e navegue para o diretório.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="6b5e0-255">Clique com o botão direito do mouse no diretório e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="6b5e0-256">Na guia **Segurança**, selecione o botão **Editar** e, em seguida, no botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="6b5e0-257">Clique no botão **Locais** e verifique se o sistema está selecionado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="6b5e0-258">Insira o `IIS AppPool\{APP POOL NAME}` formato, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos, em **digite os nomes de objeto para selecionar a** área.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="6b5e0-259">Selecione o botão **Verificar Nomes**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-259">Select the **Check Names** button.</span></span> <span data-ttu-id="6b5e0-260">Para o *DefaultAppPool* , verifique os nomes usando `IIS AppPool\DefaultAppPool` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="6b5e0-261">Quando o botão **verificar nomes** é selecionado, um valor de `DefaultAppPool` é indicado na área nomes de objetos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="6b5e0-262">Não é possível inserir o nome do pool de aplicativos diretamente na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="6b5e0-263">Use o `IIS AppPool\{APP POOL NAME}` formato, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos, ao verificar o nome do objeto.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: o nome do pool de aplicativos "DefaultAppPool" é anexado ao "IIS AppPool\" na área de nomes de objeto antes de selecionar"Verificar Nomes".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="6b5e0-265">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-265">Select **OK**.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: depois de selecionar "Verificar Nomes", o nome do objeto "DefaultAppPool" é mostrado na área de nomes de objeto.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="6b5e0-267">As permissões de leitura &amp; execução devem ser concedidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="6b5e0-268">Forneça permissões adicionais conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="6b5e0-269">O acesso também pode ser concedido por meio de um prompt de comando usando a ferramenta **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="6b5e0-270">Usando o *DefaultAppPool* como exemplo, o comando a seguir é usado:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="6b5e0-271">Para saber mais, veja o tópico [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="6b5e0-272">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6b5e0-272">HTTP/2 support</span></span>

<span data-ttu-id="6b5e0-273">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="6b5e0-274">Em processo</span><span class="sxs-lookup"><span data-stu-id="6b5e0-274">In-process</span></span>
  * <span data-ttu-id="6b5e0-275">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="6b5e0-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6b5e0-276">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="6b5e0-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="6b5e0-277">Fora do processo</span><span class="sxs-lookup"><span data-stu-id="6b5e0-277">Out-of-process</span></span>
  * <span data-ttu-id="6b5e0-278">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="6b5e0-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6b5e0-279">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="6b5e0-280">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="6b5e0-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6b5e0-281">Para uma implantação em processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="6b5e0-282">Para uma implantação fora do processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="6b5e0-283">Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="6b5e0-284">O HTTP/2 está habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="6b5e0-285">As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="6b5e0-286">Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="6b5e0-287">Solicitações de simulação do CORS</span><span class="sxs-lookup"><span data-stu-id="6b5e0-287">CORS preflight requests</span></span>

<span data-ttu-id="6b5e0-288">*Esta seção só se aplica a aplicativos ASP.NET Core com o .NET Framework como destino.*</span><span class="sxs-lookup"><span data-stu-id="6b5e0-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="6b5e0-289">Para um aplicativo ASP.NET Core com o .NET Framework como destino, as solicitações OPTIONS não são passadas para o aplicativo por padrão no IIS.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="6b5e0-290">Para saber como configurar os manipuladores do IIS do aplicativo no `web.config` para passar solicitações de opções, consulte [habilitar solicitações entre origens no ASP.NET Web API 2: como o CORS funciona](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="6b5e0-291">Módulo de Inicialização de Aplicativo e Tempo Limite de Ociosidade</span><span class="sxs-lookup"><span data-stu-id="6b5e0-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="6b5e0-292">Quando hospedado no IIS pela versão 2 do Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="6b5e0-293">[Módulo de inicialização do aplicativo](#application-initialization-module): hospedado [em processo](xref:host-and-deploy/iis/in-process-hosting) ou [fora do processo](xref:host-and-deploy/iis/out-of-process-hosting) do aplicativo pode ser configurado para iniciar automaticamente em uma reinicialização do processo de trabalho ou reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="6b5e0-294">[Tempo limite de ociosidade](#idle-timeout): o aplicativo hospedado [no processo](xref:host-and-deploy/iis/in-process-hosting) pode ser configurado para não atingir o tempo limite durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="6b5e0-295">Módulo de Inicialização de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="6b5e0-295">Application Initialization Module</span></span>

<span data-ttu-id="6b5e0-296">*Aplica-se a aplicativos hospedados em processo e fora de processo.*</span><span class="sxs-lookup"><span data-stu-id="6b5e0-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="6b5e0-297">[Inicialização de Aplicativo IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) é um recurso do IIS que envia uma solicitação HTTP para o aplicativo quando o pool de aplicativos é iniciado ou reciclado.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="6b5e0-298">A solicitação dispara a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-298">The request triggers the app to start.</span></span> <span data-ttu-id="6b5e0-299">Por padrão, o IIS emite uma solicitação para a URL raiz do aplicativo (`/`) para inicializar o aplicativo (confira mais detalhes sobre a configuração nos [recursos adicionais](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="6b5e0-300">Confirme se o recurso da função Inicialização de Aplicativo do IIS está habilitado:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="6b5e0-301">No Windows 7 ou sistemas de área de trabalho posteriores, ao usar o IIS localmente:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="6b5e0-302">Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="6b5e0-303">Abra **serviços de informações da Internet**  >  recursos de desenvolvimento de aplicativos**dos serviços World Wide Web**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="6b5e0-304">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="6b5e0-305">No Windows Server 2008 R2 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="6b5e0-306">Abra o **Assistente de Adição de Funções e Recursos**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="6b5e0-307">No painel **Selecionar serviços de função**, abra o nó **Desenvolvimento de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="6b5e0-308">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="6b5e0-309">Use quaisquer das abordagens a seguir para habilitar o Módulo de Inicialização do Aplicativo para o site:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="6b5e0-310">Usando o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="6b5e0-311">Selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="6b5e0-312">Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="6b5e0-313">O **modo de início** padrão é `OnDemand` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="6b5e0-314">Defina o **modo de início** como `AlwaysRunning` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="6b5e0-315">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-315">Select **OK**.</span></span>
  1. <span data-ttu-id="6b5e0-316">Abra o nó **Sites** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="6b5e0-317">Clique com o botão direito do mouse no aplicativo e selecione **gerenciar**  >  **Configurações avançadas**do site.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="6b5e0-318">A configuração de **pré-carregamento habilitada** padrão é `False` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="6b5e0-319">Defina **pré-carregamento habilitado** para `True` .</span><span class="sxs-lookup"><span data-stu-id="6b5e0-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="6b5e0-320">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-320">Select **OK**.</span></span>

* <span data-ttu-id="6b5e0-321">Usando `web.config` , adicione o `<applicationInitialization>` elemento com `doAppInitAfterRestart` definido como `true` para os `<system.webServer>` elementos no arquivo do aplicativo `web.config` :</span><span class="sxs-lookup"><span data-stu-id="6b5e0-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="6b5e0-322">Tempo limite de ociosidade</span><span class="sxs-lookup"><span data-stu-id="6b5e0-322">Idle Timeout</span></span>

<span data-ttu-id="6b5e0-323">*Só se aplica a aplicativos hospedados em processo.*</span><span class="sxs-lookup"><span data-stu-id="6b5e0-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="6b5e0-324">Para impedir que o aplicativo fique ocioso, defina o tempo limite de ociosidade do pool de aplicativos, usando o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="6b5e0-325">Selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="6b5e0-326">Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="6b5e0-327">O **tempo limite de ociosidade padrão (minutos)** é `20` minutos.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="6b5e0-328">Defina o **tempo limite de ociosidade (minutos)** como `0` (zero).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="6b5e0-329">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-329">Select **OK**.</span></span>
1. <span data-ttu-id="6b5e0-330">Recicle o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-330">Recycle the worker process.</span></span>

<span data-ttu-id="6b5e0-331">Para impedir que aplicativos hospedados [fora de processo](xref:host-and-deploy/iis/out-of-process-hosting) atinjam o tempo limite, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="6b5e0-332">Execute ping do aplicativo de um serviço externo para mantê-lo em execução.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="6b5e0-333">Se o aplicativo hospedar apenas serviços em segundo plano, evite a hospedagem do IIS e use um [Serviço do Windows para hospedar o aplicativo ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="6b5e0-334">Recursos adicionais do Módulo de Inicialização de Aplicativo e do Tempo Limite de Ociosidade</span><span class="sxs-lookup"><span data-stu-id="6b5e0-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="6b5e0-335">Inicialização de Aplicativo do IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="6b5e0-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="6b5e0-336">[Inicialização `<applicationInitialization>` do aplicativo ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="6b5e0-337">[Configurações do modelo de processo para um `<processModel>` pool de aplicativos ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="6b5e0-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="6b5e0-338">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="6b5e0-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="6b5e0-339">Módulo</span><span class="sxs-lookup"><span data-stu-id="6b5e0-339">Module</span></span>

<span data-ttu-id="6b5e0-340">**IIS (x86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="6b5e0-341">**IIS Express (x86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="6b5e0-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="6b5e0-342">Esquema</span><span class="sxs-lookup"><span data-stu-id="6b5e0-342">Schema</span></span>

<span data-ttu-id="6b5e0-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="6b5e0-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="6b5e0-345">Configuração</span><span class="sxs-lookup"><span data-stu-id="6b5e0-345">Configuration</span></span>

<span data-ttu-id="6b5e0-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="6b5e0-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6b5e0-347">**IIS Express**</span></span>

* <span data-ttu-id="6b5e0-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="6b5e0-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="6b5e0-349">*iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="6b5e0-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="6b5e0-350">Os arquivos podem ser encontrados pesquisando `aspnetcore` no `applicationHost.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="6b5e0-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
