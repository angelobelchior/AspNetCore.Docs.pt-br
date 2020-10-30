---
title: 'Compartilhar s de autenticação :::no-loc(cookie)::: entre aplicativos ASP.net'
author: rick-anderson
description: 'Saiba como compartilhar os s de autenticação :::no-loc(cookie)::: entre os aplicativos ASP.NET 4. x e ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059683"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="4d061-103">Compartilhar s de autenticação :::no-loc(cookie)::: entre aplicativos ASP.net</span><span class="sxs-lookup"><span data-stu-id="4d061-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="4d061-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d061-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4d061-105">Os sites geralmente consistem em aplicativos Web individuais trabalhando juntos.</span><span class="sxs-lookup"><span data-stu-id="4d061-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="4d061-106">Para fornecer uma experiência de logon único (SSO), os aplicativos Web em um site devem compartilhar os :::no-loc(cookie)::: s de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4d061-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="4d061-107">Para dar suporte a esse cenário, a pilha de proteção de dados permite o compartilhamento de :::no-loc(cookie)::: autenticação Katana e :::no-loc(cookie)::: tíquetes de autenticação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d061-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="4d061-108">Nos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4d061-108">In the examples that follow:</span></span>

* <span data-ttu-id="4d061-109">O nome de autenticação :::no-loc(cookie)::: é definido como um valor comum de `.AspNet.Shared:::no-loc(Cookie):::` .</span><span class="sxs-lookup"><span data-stu-id="4d061-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="4d061-110">O `AuthenticationType` é definido como `:::no-loc(Identity):::.Application` explicitamente ou por padrão.</span><span class="sxs-lookup"><span data-stu-id="4d061-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="4d061-111">Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados Compartilhe chaves de proteção de dados ( `Shared:::no-loc(Cookie):::App` ).</span><span class="sxs-lookup"><span data-stu-id="4d061-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="4d061-112">`:::no-loc(Identity):::.Application` é usado como o esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4d061-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="4d061-113">Seja qual for o esquema usado, ele deve ser usado consistentemente *dentro e entre* os :::no-loc(cookie)::: aplicativos compartilhados como o esquema padrão ou definindo-o explicitamente.</span><span class="sxs-lookup"><span data-stu-id="4d061-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="4d061-114">O esquema é usado ao criptografar e descriptografar :::no-loc(cookie)::: s, portanto, um esquema consistente deve ser usado em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4d061-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="4d061-115">Um local de armazenamento de [chave de proteção de dados](xref:security/data-protection/implementation/key-management) comum é usado.</span><span class="sxs-lookup"><span data-stu-id="4d061-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="4d061-116">Em ASP.NET Core aplicativos, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> é usado para definir o local de armazenamento de chaves.</span><span class="sxs-lookup"><span data-stu-id="4d061-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="4d061-117">Em aplicativos .NET Framework, :::no-loc(Cookie)::: o middleware de autenticação usa uma implementação de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="4d061-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="4d061-118">`DataProtectionProvider` fornece serviços de proteção de dados para criptografia e descriptografia de :::no-loc(cookie)::: dados de carga de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4d061-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="4d061-119">A `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d061-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="4d061-120">[Dataprotectionprovider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) aceita um <xref:System.IO.DirectoryInfo> para especificar o local para armazenamento de chave de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="4d061-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="4d061-121">`DataProtectionProvider` requer o pacote NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="4d061-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="4d061-122">Em aplicativos ASP.NET Core 2. x, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4d061-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="4d061-123">Em .NET Framework aplicativos, adicione uma referência de pacote a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="4d061-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="4d061-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> define o nome comum do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d061-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="4d061-125">Compartilhar :::no-loc(cookie)::: s de autenticação com :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="4d061-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="4d061-126">Ao usar :::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="4d061-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="4d061-127">As chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4d061-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="4d061-128">Um local de armazenamento de chave comum é fornecido para o <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="4d061-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="4d061-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar um nome de aplicativo compartilhado comum ( `Shared:::no-loc(Cookie):::App` nos exemplos a seguir).</span><span class="sxs-lookup"><span data-stu-id="4d061-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="4d061-130">Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="4d061-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="4d061-131">Use o <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> método de extensão para configurar o serviço de proteção de dados para :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="4d061-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="4d061-132">O tipo de autenticação padrão é `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="4d061-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="4d061-133">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4d061-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="4d061-134">Compartilhar s de autenticação :::no-loc(cookie)::: sem :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="4d061-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="4d061-135">Ao usar :::no-loc(cookie)::: s diretamente sem :::no-loc(ASP.NET Core Identity)::: , configure a proteção de dados e a autenticação no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4d061-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4d061-136">No exemplo a seguir, o tipo de autenticação é definido como `:::no-loc(Identity):::.Application` :</span><span class="sxs-lookup"><span data-stu-id="4d061-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="4d061-137">Compartilhar :::no-loc(cookie)::: s em caminhos de base diferentes</span><span class="sxs-lookup"><span data-stu-id="4d061-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="4d061-138">Uma autenticação :::no-loc(cookie)::: usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) como padrão [ :::no-loc(Cookie)::: . Caminho](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span><span class="sxs-lookup"><span data-stu-id="4d061-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="4d061-139">Se o aplicativo :::no-loc(cookie)::: deve ser compartilhado entre caminhos base diferentes, `Path` deve ser substituído:</span><span class="sxs-lookup"><span data-stu-id="4d061-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="4d061-140">Compartilhar :::no-loc(cookie)::: s em subdomínios</span><span class="sxs-lookup"><span data-stu-id="4d061-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="4d061-141">Ao hospedar aplicativos que compartilham :::no-loc(cookie)::: s em subdomínios, especifique um domínio comum no [ :::no-loc(Cookie)::: . ](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain)Propriedade de domínio.</span><span class="sxs-lookup"><span data-stu-id="4d061-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="4d061-142">Para compartilhar :::no-loc(cookie)::: s entre aplicativos em `contoso.com` , como `first_subdomain.contoso.com` e `second_subdomain.contoso.com` , especifique o `:::no-loc(Cookie):::.Domain` como `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="4d061-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="4d061-143">Criptografar chaves de proteção de dados em repouso</span><span class="sxs-lookup"><span data-stu-id="4d061-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="4d061-144">Para implantações de produção, configure o `DataProtectionProvider` para criptografar chaves em repouso com DPAPI ou um X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="4d061-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="4d061-145">Para obter mais informações, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="4d061-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="4d061-146">No exemplo a seguir, uma impressão digital do certificado é fornecida para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="4d061-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="4d061-147">Compartilhar os :::no-loc(cookie)::: s de autenticação entre os aplicativos ASP.NET 4. x e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d061-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="4d061-148">Os aplicativos ASP.NET 4. x que usam :::no-loc(Cookie)::: o middleware de autenticação Katana podem ser configurados para gerar autenticação :::no-loc(cookie)::: s que são compatíveis com o :::no-loc(Cookie)::: middleware de autenticação ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d061-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="4d061-149">Isso permite atualizar aplicativos individuais de um site grande em várias etapas, ao mesmo tempo em que fornece uma experiência de SSO tranqüila em todo o site.</span><span class="sxs-lookup"><span data-stu-id="4d061-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="4d061-150">Quando um aplicativo usa :::no-loc(Cookie)::: o middleware de autenticação Katana, ele chama `Use:::no-loc(Cookie):::Authentication` o arquivo *Startup.auth.cs* do projeto.</span><span class="sxs-lookup"><span data-stu-id="4d061-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="4d061-151">Os projetos de aplicativo Web ASP.NET 4. x criados com Visual Studio 2013 e posterior usam o :::no-loc(Cookie)::: middleware de autenticação Katana por padrão.</span><span class="sxs-lookup"><span data-stu-id="4d061-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="4d061-152">Embora `Use:::no-loc(Cookie):::Authentication` o seja obsoleto e não tenha suporte para aplicativos ASP.NET Core, chamar `Use:::no-loc(Cookie):::Authentication` um aplicativo ASP.NET 4. x que usa o :::no-loc(Cookie)::: middleware de autenticação Katana é válido.</span><span class="sxs-lookup"><span data-stu-id="4d061-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="4d061-153">Um aplicativo ASP.NET 4. x deve ter como destino .NET Framework 4.5.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="4d061-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="4d061-154">Caso contrário, os pacotes NuGet necessários falharão na instalação.</span><span class="sxs-lookup"><span data-stu-id="4d061-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="4d061-155">Para compartilhar :::no-loc(cookie)::: os s de autenticação entre um aplicativo ASP.NET 4. x e um aplicativo ASP.NET Core, configure o aplicativo ASP.NET Core conforme indicado na seção [s de autenticação de compartilhamento :::no-loc(cookie)::: entre ASP.NET Core aplicativos](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) e, em seguida, configure o aplicativo ASP.NET 4. x da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="4d061-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="4d061-156">Confirme se os pacotes do aplicativo foram atualizados para as versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="4d061-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="4d061-157">Instale o pacote [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) em cada aplicativo ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="4d061-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="4d061-158">Localize e modifique a chamada para `Use:::no-loc(Cookie):::Authentication` :</span><span class="sxs-lookup"><span data-stu-id="4d061-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="4d061-159">Altere o :::no-loc(cookie)::: nome para corresponder ao nome usado pelo middleware de :::no-loc(Cookie)::: autenticação ASP.NET Core ( `.AspNet.Shared:::no-loc(Cookie):::` no exemplo).</span><span class="sxs-lookup"><span data-stu-id="4d061-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="4d061-160">No exemplo a seguir, o tipo de autenticação é definido como `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="4d061-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="4d061-161">Forneça uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chave de proteção de dados comum.</span><span class="sxs-lookup"><span data-stu-id="4d061-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="4d061-162">Confirme se o nome do aplicativo está definido como o nome do aplicativo comum usado por todos os aplicativos que compartilham os s de autenticação :::no-loc(cookie)::: ( `Shared:::no-loc(Cookie):::App` no exemplo).</span><span class="sxs-lookup"><span data-stu-id="4d061-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="4d061-163">Se não estiver configurando `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , defina <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> como uma declaração que diferencie usuários exclusivos.</span><span class="sxs-lookup"><span data-stu-id="4d061-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="4d061-164">*App_Start/Startup.auth.cs* :</span><span class="sxs-lookup"><span data-stu-id="4d061-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="4d061-165">Ao gerar uma identidade de usuário, o tipo de autenticação ( `:::no-loc(Identity):::.Application` ) deve corresponder ao tipo definido em `AuthenticationType` conjunto com `Use:::no-loc(Cookie):::Authentication` em *App_Start/Startup.auth.cs* .</span><span class="sxs-lookup"><span data-stu-id="4d061-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="4d061-166">*Modelos/ :::no-loc(Identity)::: Models.cs* :</span><span class="sxs-lookup"><span data-stu-id="4d061-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="4d061-167">Usar um banco de dados de usuário comum</span><span class="sxs-lookup"><span data-stu-id="4d061-167">Use a common user database</span></span>

<span data-ttu-id="4d061-168">Quando os aplicativos usam o mesmo :::no-loc(Identity)::: esquema (mesma versão do :::no-loc(Identity)::: ), confirme se o :::no-loc(Identity)::: sistema de cada aplicativo está apontado para o mesmo banco de dados de usuário.</span><span class="sxs-lookup"><span data-stu-id="4d061-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="4d061-169">Caso contrário, o sistema de identidade produz falhas em tempo de execução quando tenta corresponder as informações na autenticação em :::no-loc(cookie)::: relação às informações em seu banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4d061-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="4d061-170">Quando o :::no-loc(Identity)::: esquema é diferente entre os aplicativos, geralmente porque os aplicativos estão usando :::no-loc(Identity)::: versões diferentes, compartilhar um banco de dados comum com base na versão mais recente do :::no-loc(Identity)::: não é possível sem remapear e adicionar colunas nos esquemas de outros aplicativos :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="4d061-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="4d061-171">Geralmente, é mais eficiente atualizar os outros aplicativos para usar a versão mais recente :::no-loc(Identity)::: para que um banco de dados comum possa ser compartilhado pelos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4d061-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d061-172">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4d061-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
