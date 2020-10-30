---
title: Manter declarações e tokens adicionais de provedores externos no ASP.NET Core
author: rick-anderson
description: Saiba como estabelecer declarações e tokens adicionais de provedores externos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 733afec8d3253ec58a7edf6d7fcf35e303a7fe57
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060320"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="54ee0-103">Manter declarações e tokens adicionais de provedores externos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54ee0-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54ee0-104">Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="54ee0-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="54ee0-105">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="54ee0-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54ee0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54ee0-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54ee0-107">Prerequisites</span></span>

<span data-ttu-id="54ee0-108">Decida quais provedores de autenticação externa dar suporte no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="54ee0-109">Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="54ee0-110">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="54ee0-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="54ee0-111">O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="54ee0-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="54ee0-112">Definir a ID do cliente e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="54ee0-112">Set the client ID and client secret</span></span>

<span data-ttu-id="54ee0-113">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="54ee0-114">Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="54ee0-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="54ee0-115">Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="54ee0-116">Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="54ee0-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="54ee0-117">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="54ee0-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="54ee0-118">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="54ee0-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="54ee0-119">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="54ee0-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="54ee0-120">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="54ee0-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="54ee0-121">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="54ee0-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="54ee0-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="54ee0-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="54ee0-123">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:</span><span class="sxs-lookup"><span data-stu-id="54ee0-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="54ee0-124">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="54ee0-124">Establish the authentication scope</span></span>

<span data-ttu-id="54ee0-125">Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="54ee0-126">Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="54ee0-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="54ee0-127">Provedor</span><span class="sxs-lookup"><span data-stu-id="54ee0-127">Provider</span></span>  | <span data-ttu-id="54ee0-128">Escopo</span><span class="sxs-lookup"><span data-stu-id="54ee0-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="54ee0-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="54ee0-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="54ee0-130">Google</span><span class="sxs-lookup"><span data-stu-id="54ee0-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="54ee0-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="54ee0-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="54ee0-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="54ee0-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="54ee0-133">No aplicativo de exemplo, o escopo do Google `userinfo.profile` é adicionado automaticamente pelo Framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="54ee0-134">Se o aplicativo exigir escopos adicionais, adicione-os às opções.</span><span class="sxs-lookup"><span data-stu-id="54ee0-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="54ee0-135">No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:</span><span class="sxs-lookup"><span data-stu-id="54ee0-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="54ee0-136">Mapear chaves de dados do usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="54ee0-136">Map user data keys and create claims</span></span>

<span data-ttu-id="54ee0-137">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada.</span><span class="sxs-lookup"><span data-stu-id="54ee0-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="54ee0-138">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="54ee0-139">O aplicativo de exemplo cria `urn:google:locale` declarações de localidade () e de imagem ( `urn:google:picture` ) das `locale` `picture` chaves e nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="54ee0-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="54ee0-140">No `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , um <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-140">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="54ee0-141">Durante o processo de entrada, o <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> pode armazenar uma `ApplicationUser` declaração de dados do usuário disponível no <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-141">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="54ee0-142">No aplicativo de exemplo, `OnPostConfirmationAsync` ( *Account/ExternalLogin. cshtml. cs* ) estabelece as declarações de locale ( `urn:google:locale` ) e Picture ( `urn:google:picture` ) para o conectado `ApplicationUser` , incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="54ee0-142">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="54ee0-143">Por padrão, as declarações de um usuário são armazenadas na autenticação do :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54ee0-143">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="54ee0-144">Se a autenticação :::no-loc(cookie)::: for muito grande, isso poderá causar falha no aplicativo porque:</span><span class="sxs-lookup"><span data-stu-id="54ee0-144">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="54ee0-145">O navegador detecta que o :::no-loc(cookie)::: cabeçalho é muito longo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-145">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="54ee0-146">O tamanho geral da solicitação é muito grande.</span><span class="sxs-lookup"><span data-stu-id="54ee0-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="54ee0-147">Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:</span><span class="sxs-lookup"><span data-stu-id="54ee0-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="54ee0-148">Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="54ee0-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="54ee0-149">Use um personalizado <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> para o :::no-loc(Cookie)::: middleware de autenticação <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="54ee0-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="54ee0-150">Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="54ee0-151">Salvar o token de acesso</span><span class="sxs-lookup"><span data-stu-id="54ee0-151">Save the access token</span></span>

<span data-ttu-id="54ee0-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define se os tokens de acesso e de atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="54ee0-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="54ee0-153">`SaveTokens` é definido como `false` por padrão para reduzir o tamanho da autenticação final :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54ee0-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="54ee0-154">O aplicativo de exemplo define o valor de `SaveTokens` para `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="54ee0-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="54ee0-155">Quando `OnPostConfirmationAsync` o é executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo `ApplicationUser` no `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="54ee0-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="54ee0-156">O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="54ee0-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="54ee0-157">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="54ee0-157">How to add additional custom tokens</span></span>

<span data-ttu-id="54ee0-158">Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens` , o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="54ee0-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="54ee0-159">Criando e adicionando declarações</span><span class="sxs-lookup"><span data-stu-id="54ee0-159">Creating and adding claims</span></span>

<span data-ttu-id="54ee0-160">A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção.</span><span class="sxs-lookup"><span data-stu-id="54ee0-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="54ee0-161">Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="54ee0-162">Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstract.</span><span class="sxs-lookup"><span data-stu-id="54ee0-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="54ee0-163">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="54ee0-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="54ee0-164">Remoção de declarações e ações de declaração</span><span class="sxs-lookup"><span data-stu-id="54ee0-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="54ee0-165">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o dado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção.</span><span class="sxs-lookup"><span data-stu-id="54ee0-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="54ee0-166">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do dado da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> identidade.</span><span class="sxs-lookup"><span data-stu-id="54ee0-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="54ee0-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="54ee0-168">Saída do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="54ee0-168">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54ee0-169">Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="54ee0-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="54ee0-170">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="54ee0-171">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54ee0-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54ee0-172">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54ee0-172">Prerequisites</span></span>

<span data-ttu-id="54ee0-173">Decida quais provedores de autenticação externa dar suporte no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="54ee0-174">Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="54ee0-175">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="54ee0-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="54ee0-176">O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="54ee0-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="54ee0-177">Definir a ID do cliente e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="54ee0-177">Set the client ID and client secret</span></span>

<span data-ttu-id="54ee0-178">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="54ee0-179">Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="54ee0-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="54ee0-180">Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="54ee0-181">Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="54ee0-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="54ee0-182">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="54ee0-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="54ee0-183">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="54ee0-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="54ee0-184">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="54ee0-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="54ee0-185">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="54ee0-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="54ee0-186">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="54ee0-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="54ee0-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="54ee0-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="54ee0-188">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:</span><span class="sxs-lookup"><span data-stu-id="54ee0-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="54ee0-189">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="54ee0-189">Establish the authentication scope</span></span>

<span data-ttu-id="54ee0-190">Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="54ee0-191">Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="54ee0-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="54ee0-192">Provedor</span><span class="sxs-lookup"><span data-stu-id="54ee0-192">Provider</span></span>  | <span data-ttu-id="54ee0-193">Escopo</span><span class="sxs-lookup"><span data-stu-id="54ee0-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="54ee0-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="54ee0-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="54ee0-195">Google</span><span class="sxs-lookup"><span data-stu-id="54ee0-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="54ee0-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="54ee0-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="54ee0-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="54ee0-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="54ee0-198">No aplicativo de exemplo, o escopo do Google `userinfo.profile` é adicionado automaticamente pelo Framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="54ee0-199">Se o aplicativo exigir escopos adicionais, adicione-os às opções.</span><span class="sxs-lookup"><span data-stu-id="54ee0-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="54ee0-200">No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:</span><span class="sxs-lookup"><span data-stu-id="54ee0-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="54ee0-201">Mapear chaves de dados do usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="54ee0-201">Map user data keys and create claims</span></span>

<span data-ttu-id="54ee0-202">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada.</span><span class="sxs-lookup"><span data-stu-id="54ee0-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="54ee0-203">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="54ee0-204">O aplicativo de exemplo cria `urn:google:locale` declarações de localidade () e de imagem ( `urn:google:picture` ) das `locale` `picture` chaves e nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="54ee0-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="54ee0-205">No `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , um <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-205">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="54ee0-206">Durante o processo de entrada, o <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> pode armazenar uma `ApplicationUser` declaração de dados do usuário disponível no <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-206">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="54ee0-207">No aplicativo de exemplo, `OnPostConfirmationAsync` ( *Account/ExternalLogin. cshtml. cs* ) estabelece as declarações de locale ( `urn:google:locale` ) e Picture ( `urn:google:picture` ) para o conectado `ApplicationUser` , incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="54ee0-207">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="54ee0-208">Por padrão, as declarações de um usuário são armazenadas na autenticação do :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54ee0-208">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="54ee0-209">Se a autenticação :::no-loc(cookie)::: for muito grande, isso poderá causar falha no aplicativo porque:</span><span class="sxs-lookup"><span data-stu-id="54ee0-209">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="54ee0-210">O navegador detecta que o :::no-loc(cookie)::: cabeçalho é muito longo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-210">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="54ee0-211">O tamanho geral da solicitação é muito grande.</span><span class="sxs-lookup"><span data-stu-id="54ee0-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="54ee0-212">Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:</span><span class="sxs-lookup"><span data-stu-id="54ee0-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="54ee0-213">Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="54ee0-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="54ee0-214">Use um personalizado <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> para o :::no-loc(Cookie)::: middleware de autenticação <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="54ee0-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="54ee0-215">Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.</span><span class="sxs-lookup"><span data-stu-id="54ee0-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="54ee0-216">Salvar o token de acesso</span><span class="sxs-lookup"><span data-stu-id="54ee0-216">Save the access token</span></span>

<span data-ttu-id="54ee0-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define se os tokens de acesso e de atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="54ee0-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="54ee0-218">`SaveTokens` é definido como `false` por padrão para reduzir o tamanho da autenticação final :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54ee0-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="54ee0-219">O aplicativo de exemplo define o valor de `SaveTokens` para `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="54ee0-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="54ee0-220">Quando `OnPostConfirmationAsync` o é executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo `ApplicationUser` no `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="54ee0-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="54ee0-221">O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="54ee0-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="54ee0-222">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="54ee0-222">How to add additional custom tokens</span></span>

<span data-ttu-id="54ee0-223">Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens` , o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="54ee0-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="54ee0-224">Criando e adicionando declarações</span><span class="sxs-lookup"><span data-stu-id="54ee0-224">Creating and adding claims</span></span>

<span data-ttu-id="54ee0-225">A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção.</span><span class="sxs-lookup"><span data-stu-id="54ee0-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="54ee0-226">Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="54ee0-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="54ee0-227">Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstract.</span><span class="sxs-lookup"><span data-stu-id="54ee0-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="54ee0-228">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="54ee0-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="54ee0-229">Remoção de declarações e ações de declaração</span><span class="sxs-lookup"><span data-stu-id="54ee0-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="54ee0-230">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o dado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção.</span><span class="sxs-lookup"><span data-stu-id="54ee0-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="54ee0-231">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do dado da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> identidade.</span><span class="sxs-lookup"><span data-stu-id="54ee0-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="54ee0-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.</span><span class="sxs-lookup"><span data-stu-id="54ee0-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="54ee0-233">Saída do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="54ee0-233">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="54ee0-234">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="54ee0-234">Additional resources</span></span>

* <span data-ttu-id="54ee0-235">[dotnet/AspNetCore Engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): o aplicativo de exemplo vinculado está no Branch de engenharia [do repositório do GitHub dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="54ee0-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="54ee0-236">A `master` ramificação contém código em desenvolvimento ativo para a próxima versão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54ee0-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="54ee0-237">Para ver uma versão do aplicativo de exemplo para uma versão lançada do ASP.NET Core, use a lista suspensa **Branch** para selecionar um Branch de lançamento (por exemplo `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="54ee0-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
