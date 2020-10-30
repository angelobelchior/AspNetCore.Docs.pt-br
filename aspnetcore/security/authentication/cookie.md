---
title: 'Usar :::no-loc(cookie)::: autenticação sem :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'Saiba como usar a :::no-loc(cookie)::: autenticação sem o :::no-loc(ASP.NET Core Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061347"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="e78f9-103">Usar :::no-loc(cookie)::: autenticação sem :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="e78f9-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="e78f9-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e78f9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e78f9-105">:::no-loc(ASP.NET Core Identity)::: é um provedor de autenticação completa e completo para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="e78f9-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e78f9-106">No entanto, um :::no-loc(cookie)::: provedor de autenticação com base sem :::no-loc(ASP.NET Core Identity)::: pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="e78f9-107">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e78f9-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e78f9-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e78f9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e78f9-109">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e78f9-110">Use o endereço de **email** `maria.rodriguez@contoso.com` e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f9-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e78f9-111">O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="e78f9-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e78f9-112">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e78f9-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e78f9-113">Configuração</span><span class="sxs-lookup"><span data-stu-id="e78f9-113">Configuration</span></span>

<span data-ttu-id="e78f9-114">No `Startup.ConfigureServices` método, crie os serviços de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> métodos e:</span><span class="sxs-lookup"><span data-stu-id="e78f9-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="e78f9-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e78f9-116">`AuthenticationScheme` é útil quando há várias instâncias de :::no-loc(cookie)::: autenticação e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="e78f9-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e78f9-117">A definição de `AuthenticationScheme` para [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) fornece um valor de " :::no-loc(Cookie)::: s" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="e78f9-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="e78f9-118">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="e78f9-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e78f9-119">O esquema de autenticação do aplicativo é diferente do esquema de :::no-loc(cookie)::: autenticação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="e78f9-120">Quando um :::no-loc(cookie)::: esquema de autenticação não é fornecido para o <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , ele usa `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="e78f9-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="e78f9-121">A :::no-loc(cookie)::: propriedade da autenticação <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> é definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="e78f9-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e78f9-122">:::no-loc(cookie):::Os s de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="e78f9-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e78f9-123">Para obter mais informações, consulte <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="e78f9-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="e78f9-124">No `Startup.Configure` , chame `UseAuthentication` e `UseAuthorization` para definir a `HttpContext.User` propriedade e executar o middleware de autorização para solicitações.</span><span class="sxs-lookup"><span data-stu-id="e78f9-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e78f9-125">Chame os `UseAuthentication` `UseAuthorization` métodos e antes de chamar `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e78f9-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="e78f9-126">A <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e78f9-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e78f9-127">Defina `:::no-loc(Cookie):::AuthenticationOptions` na configuração de serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="e78f9-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e78f9-128">:::no-loc(Cookie)::: Middleware de política</span><span class="sxs-lookup"><span data-stu-id="e78f9-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="e78f9-129">O [ :::no-loc(Cookie)::: middleware de política](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) habilita os :::no-loc(cookie)::: recursos de política.</span><span class="sxs-lookup"><span data-stu-id="e78f9-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="e78f9-130">A adição do middleware ao pipeline de processamento do aplicativo é a diferenciação de ordem &mdash; que afeta apenas os componentes de downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="e78f9-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="e78f9-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> o fornecido ao :::no-loc(Cookie)::: middleware de política para controlar as características globais do :::no-loc(cookie)::: processamento e o gancho em :::no-loc(cookie)::: manipuladores de processamento quando :::no-loc(cookie)::: s forem acrescentadas ou excluídas.</span><span class="sxs-lookup"><span data-stu-id="e78f9-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="e78f9-132">O <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> valor padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="e78f9-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e78f9-133">Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict` , defina o `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e78f9-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e78f9-134">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de :::no-loc(cookie)::: segurança para outros tipos de aplicativos que não dependem do processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="e78f9-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e78f9-135">A :::no-loc(Cookie)::: configuração de middleware de política para `MinimumSameSitePolicy` pode afetar a configuração de `:::no-loc(Cookie):::.SameSite` em configurações de acordo com `:::no-loc(Cookie):::AuthenticationOptions` a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e78f9-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e78f9-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e78f9-137">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="e78f9-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="e78f9-138">Resultante :::no-loc(Cookie)::: . Configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="e78f9-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e78f9-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-139">SameSiteMode.None</span></span>     | <span data-ttu-id="e78f9-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-140">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="e78f9-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-143">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e78f9-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e78f9-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-147">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="e78f9-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e78f9-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e78f9-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-154">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="e78f9-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="e78f9-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e78f9-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e78f9-160">Criar uma autenticação :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="e78f9-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="e78f9-161">Para criar :::no-loc(cookie)::: informações de usuário de retenção, construa um <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e78f9-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e78f9-162">As informações do usuário são serializadas e armazenadas no :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e78f9-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="e78f9-163">Crie um <xref:System.Security.Claims.Claims:::no-loc(Identity):::> com qualquer <xref:System.Security.Claims.Claim> s necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="e78f9-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e78f9-164">`SignInAsync` Cria um criptografado :::no-loc(cookie)::: e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="e78f9-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="e78f9-165">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e78f9-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> é usado apenas em alguns caminhos específicos por padrão, por exemplo, o caminho de logon e os caminhos de logout.</span><span class="sxs-lookup"><span data-stu-id="e78f9-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e78f9-167">Para obter mais informações, consulte a [ :::no-loc(Cookie)::: origem do AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="e78f9-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e78f9-168">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="e78f9-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e78f9-169">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e78f9-170">Sair</span><span class="sxs-lookup"><span data-stu-id="e78f9-170">Sign out</span></span>

<span data-ttu-id="e78f9-171">Para desconectar o usuário atual e excluir seu :::no-loc(cookie)::: , chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e78f9-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e78f9-172">Se `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (ou " :::no-loc(Cookie)::: s") não for usado como o esquema (por exemplo, "Contoso :::no-loc(Cookie)::: "), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e78f9-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e78f9-173">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="e78f9-174">Quando o navegador fecha, ele exclui automaticamente os s baseados em sessão :::no-loc(cookie)::: (s não persistentes :::no-loc(cookie)::: ), mas nenhum :::no-loc(cookie)::: s é limpo quando uma guia individual é fechada.</span><span class="sxs-lookup"><span data-stu-id="e78f9-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="e78f9-175">O servidor não é notificado sobre eventos de fechamento de guia ou navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f9-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e78f9-176">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="e78f9-176">React to back-end changes</span></span>

<span data-ttu-id="e78f9-177">Depois que um :::no-loc(cookie)::: é criado, o :::no-loc(cookie)::: é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="e78f9-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="e78f9-178">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="e78f9-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e78f9-179">O sistema de :::no-loc(cookie)::: autenticação do aplicativo continua processando solicitações com base na autenticação :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e78f9-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="e78f9-180">O usuário permanece conectado ao aplicativo, contanto que a autenticação :::no-loc(cookie)::: seja válida.</span><span class="sxs-lookup"><span data-stu-id="e78f9-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="e78f9-181">O <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da :::no-loc(cookie)::: identidade.</span><span class="sxs-lookup"><span data-stu-id="e78f9-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="e78f9-182">A validação do :::no-loc(cookie)::: em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e78f9-183">Uma abordagem para a :::no-loc(cookie)::: validação é baseada na manutenção do controle quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e78f9-184">Se o banco de dados não tiver sido alterado desde que o usuário :::no-loc(cookie)::: foi emitido, não haverá necessidade de autenticar novamente o usuário se ele :::no-loc(cookie)::: ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="e78f9-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="e78f9-185">No aplicativo de exemplo, o banco de dados é implementado no `IUserRepository` e armazena um `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="e78f9-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e78f9-186">Quando um usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="e78f9-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e78f9-187">Para invalidar um :::no-loc(cookie)::: quando o banco de dados for alterado com base no `LastChanged` valor, crie o :::no-loc(cookie)::: com uma `LastChanged` declaração que contém o `LastChanged` valor atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e78f9-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="e78f9-188">Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e78f9-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="e78f9-189">Veja a seguir um exemplo de implementação de `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e78f9-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="e78f9-190">Registre a instância de eventos durante :::no-loc(cookie)::: o registro de serviço no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="e78f9-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e78f9-191">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para sua `Custom:::no-loc(Cookie):::AuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="e78f9-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="e78f9-192">Considere uma situação em que o nome do usuário é atualizado &mdash; uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="e78f9-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e78f9-193">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` propriedade como `true` .</span><span class="sxs-lookup"><span data-stu-id="e78f9-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e78f9-194">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="e78f9-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e78f9-195">A validação de :::no-loc(cookie)::: s de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e78f9-196">S persistentes :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="e78f9-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="e78f9-197">Talvez você queira que o :::no-loc(cookie)::: persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f9-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="e78f9-198">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="e78f9-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e78f9-199">O trecho de código a seguir cria uma identidade e uma correspondência :::no-loc(cookie)::: que sobrevivem pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f9-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="e78f9-200">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="e78f9-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e78f9-201">Se a :::no-loc(cookie)::: expiração enquanto o navegador estiver fechado, o navegador apagará a :::no-loc(cookie)::: vez que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="e78f9-202">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e78f9-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e78f9-203">:::no-loc(cookie):::Expiração absoluta</span><span class="sxs-lookup"><span data-stu-id="e78f9-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="e78f9-204">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e78f9-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e78f9-205">Para criar um persistente :::no-loc(cookie)::: , `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="e78f9-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e78f9-206">Caso contrário, o :::no-loc(cookie)::: é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="e78f9-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e78f9-207">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> , se definido.</span><span class="sxs-lookup"><span data-stu-id="e78f9-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e78f9-208">O trecho de código a seguir cria uma identidade e correspondente :::no-loc(cookie)::: que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="e78f9-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="e78f9-209">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e78f9-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e78f9-210">:::no-loc(ASP.NET Core Identity)::: é um provedor de autenticação completa e completo para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="e78f9-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e78f9-211">No entanto, um :::no-loc(cookie)::: provedor de autenticação com base sem :::no-loc(ASP.NET Core Identity)::: pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="e78f9-212">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e78f9-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e78f9-213">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e78f9-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e78f9-214">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e78f9-215">Use o endereço de **email** `maria.rodriguez@contoso.com` e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f9-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e78f9-216">O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="e78f9-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e78f9-217">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e78f9-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e78f9-218">Configuração</span><span class="sxs-lookup"><span data-stu-id="e78f9-218">Configuration</span></span>

<span data-ttu-id="e78f9-219">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o [Microsoft. AspNetCore. Authentication. :::no-loc(Cookie)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) pacote de s.</span><span class="sxs-lookup"><span data-stu-id="e78f9-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="e78f9-220">No `Startup.ConfigureServices` método, crie o serviço de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> métodos e:</span><span class="sxs-lookup"><span data-stu-id="e78f9-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="e78f9-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e78f9-222">`AuthenticationScheme` é útil quando há várias instâncias de :::no-loc(cookie)::: autenticação e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="e78f9-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e78f9-223">A definição de `AuthenticationScheme` para [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) fornece um valor de " :::no-loc(Cookie)::: s" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="e78f9-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="e78f9-224">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="e78f9-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e78f9-225">O esquema de autenticação do aplicativo é diferente do esquema de :::no-loc(cookie)::: autenticação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="e78f9-226">Quando um :::no-loc(cookie)::: esquema de autenticação não é fornecido para o <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , ele usa `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="e78f9-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="e78f9-227">A :::no-loc(cookie)::: propriedade da autenticação <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> é definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="e78f9-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e78f9-228">:::no-loc(cookie):::Os s de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="e78f9-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e78f9-229">Para obter mais informações, consulte <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="e78f9-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="e78f9-230">No `Startup.Configure` método, chame o `UseAuthentication` método para invocar o middleware de autenticação que define a `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="e78f9-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e78f9-231">Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="e78f9-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="e78f9-232">A <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e78f9-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e78f9-233">Defina `:::no-loc(Cookie):::AuthenticationOptions` na configuração de serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="e78f9-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e78f9-234">:::no-loc(Cookie)::: Middleware de política</span><span class="sxs-lookup"><span data-stu-id="e78f9-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="e78f9-235">O [ :::no-loc(Cookie)::: middleware de política](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) habilita os :::no-loc(cookie)::: recursos de política.</span><span class="sxs-lookup"><span data-stu-id="e78f9-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="e78f9-236">A adição do middleware ao pipeline de processamento do aplicativo é a diferenciação de ordem &mdash; que afeta apenas os componentes de downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="e78f9-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="e78f9-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> o fornecido ao :::no-loc(Cookie)::: middleware de política para controlar as características globais do :::no-loc(cookie)::: processamento e o gancho em :::no-loc(cookie)::: manipuladores de processamento quando :::no-loc(cookie)::: s forem acrescentadas ou excluídas.</span><span class="sxs-lookup"><span data-stu-id="e78f9-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="e78f9-238">O <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> valor padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="e78f9-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e78f9-239">Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict` , defina o `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e78f9-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e78f9-240">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de :::no-loc(cookie)::: segurança para outros tipos de aplicativos que não dependem do processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="e78f9-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e78f9-241">A :::no-loc(Cookie)::: configuração de middleware de política para `MinimumSameSitePolicy` pode afetar a configuração de `:::no-loc(Cookie):::.SameSite` em configurações de acordo com `:::no-loc(Cookie):::AuthenticationOptions` a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e78f9-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e78f9-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e78f9-243">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="e78f9-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="e78f9-244">Resultante :::no-loc(Cookie)::: . Configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="e78f9-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e78f9-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-245">SameSiteMode.None</span></span>     | <span data-ttu-id="e78f9-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-246">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="e78f9-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-249">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e78f9-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e78f9-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-253">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="e78f9-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e78f9-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e78f9-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e78f9-260">SameSiteMode.None</span></span><br><span data-ttu-id="e78f9-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e78f9-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="e78f9-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="e78f9-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="e78f9-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="e78f9-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e78f9-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e78f9-266">Criar uma autenticação :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="e78f9-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="e78f9-267">Para criar :::no-loc(cookie)::: informações de usuário de retenção, construa um <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e78f9-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e78f9-268">As informações do usuário são serializadas e armazenadas no :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e78f9-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="e78f9-269">Crie um <xref:System.Security.Claims.Claims:::no-loc(Identity):::> com qualquer <xref:System.Security.Claims.Claim> s necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="e78f9-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e78f9-270">`SignInAsync` Cria um criptografado :::no-loc(cookie)::: e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="e78f9-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="e78f9-271">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e78f9-272">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="e78f9-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e78f9-273">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e78f9-274">Sair</span><span class="sxs-lookup"><span data-stu-id="e78f9-274">Sign out</span></span>

<span data-ttu-id="e78f9-275">Para desconectar o usuário atual e excluir seu :::no-loc(cookie)::: , chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e78f9-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e78f9-276">Se `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (ou " :::no-loc(Cookie)::: s") não for usado como o esquema (por exemplo, "Contoso :::no-loc(Cookie)::: "), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e78f9-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e78f9-277">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e78f9-278">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="e78f9-278">React to back-end changes</span></span>

<span data-ttu-id="e78f9-279">Depois que um :::no-loc(cookie)::: é criado, o :::no-loc(cookie)::: é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="e78f9-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="e78f9-280">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="e78f9-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e78f9-281">O sistema de :::no-loc(cookie)::: autenticação do aplicativo continua processando solicitações com base na autenticação :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e78f9-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="e78f9-282">O usuário permanece conectado ao aplicativo, contanto que a autenticação :::no-loc(cookie)::: seja válida.</span><span class="sxs-lookup"><span data-stu-id="e78f9-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="e78f9-283">O <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da :::no-loc(cookie)::: identidade.</span><span class="sxs-lookup"><span data-stu-id="e78f9-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="e78f9-284">A validação do :::no-loc(cookie)::: em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e78f9-285">Uma abordagem para a :::no-loc(cookie)::: validação é baseada na manutenção do controle quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e78f9-286">Se o banco de dados não tiver sido alterado desde que o usuário :::no-loc(cookie)::: foi emitido, não haverá necessidade de autenticar novamente o usuário se ele :::no-loc(cookie)::: ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="e78f9-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="e78f9-287">No aplicativo de exemplo, o banco de dados é implementado no `IUserRepository` e armazena um `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="e78f9-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e78f9-288">Quando um usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="e78f9-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e78f9-289">Para invalidar um :::no-loc(cookie)::: quando o banco de dados for alterado com base no `LastChanged` valor, crie o :::no-loc(cookie)::: com uma `LastChanged` declaração que contém o `LastChanged` valor atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e78f9-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="e78f9-290">Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e78f9-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="e78f9-291">Veja a seguir um exemplo de implementação de `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e78f9-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="e78f9-292">Registre a instância de eventos durante :::no-loc(cookie)::: o registro de serviço no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="e78f9-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e78f9-293">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para sua `Custom:::no-loc(Cookie):::AuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="e78f9-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="e78f9-294">Considere uma situação em que o nome do usuário é atualizado &mdash; uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="e78f9-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e78f9-295">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` propriedade como `true` .</span><span class="sxs-lookup"><span data-stu-id="e78f9-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e78f9-296">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="e78f9-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e78f9-297">A validação de :::no-loc(cookie)::: s de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78f9-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e78f9-298">S persistentes :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="e78f9-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="e78f9-299">Talvez você queira que o :::no-loc(cookie)::: persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f9-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="e78f9-300">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="e78f9-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e78f9-301">O trecho de código a seguir cria uma identidade e uma correspondência :::no-loc(cookie)::: que sobrevivem pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f9-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="e78f9-302">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="e78f9-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e78f9-303">Se a :::no-loc(cookie)::: expiração enquanto o navegador estiver fechado, o navegador apagará a :::no-loc(cookie)::: vez que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e78f9-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="e78f9-304">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e78f9-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e78f9-305">:::no-loc(cookie):::Expiração absoluta</span><span class="sxs-lookup"><span data-stu-id="e78f9-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="e78f9-306">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e78f9-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e78f9-307">Para criar um persistente :::no-loc(cookie)::: , `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="e78f9-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e78f9-308">Caso contrário, o :::no-loc(cookie)::: é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="e78f9-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e78f9-309">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> , se definido.</span><span class="sxs-lookup"><span data-stu-id="e78f9-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e78f9-310">O trecho de código a seguir cria uma identidade e correspondente :::no-loc(cookie)::: que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="e78f9-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="e78f9-311">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e78f9-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e78f9-312">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e78f9-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e78f9-313">Verificações de função baseadas em política</span><span class="sxs-lookup"><span data-stu-id="e78f9-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
