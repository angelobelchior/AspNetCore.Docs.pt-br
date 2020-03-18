---
title: ASP.NET Core seguro Blazor Webassembly
author: guardrex
description: Saiba como proteger Blazor aplicativos WebAssemlby como SPAs (aplicativos de página única).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 48136d0717998df3311dd5177688e062d0009176
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083835"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="8af75-103">ASP.NET Core seguro Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="8af75-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="8af75-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="8af75-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="8af75-105"> aplicativos Webassembly são protegidos da mesma maneira que os aplicativos de página única (SPAs).</span><span class="sxs-lookup"><span data-stu-id="8af75-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="8af75-106">Há várias abordagens para autenticar usuários no SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo oAuth 2,0](https://oauth.net/), como o [Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="8af75-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [oAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="8af75-107">Biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="8af75-107">Authentication library</span></span>

Blazor<span data-ttu-id="8af75-108"> Webassembly dá suporte à autenticação e à autorização de aplicativos usando o OIDC por meio da biblioteca `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="8af75-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="8af75-109">A biblioteca fornece um conjunto de primitivos para autenticação direta contra back-ends ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8af75-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="8af75-110">A biblioteca integra ASP.NET Core identidade com suporte à autorização de API criado com base no [servidor de identidade](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="8af75-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="8af75-111">A biblioteca pode se autenticar em qualquer provedor de identidade de terceiros (IP) que ofereça suporte a OIDC, que são chamadas de provedores de OpenID (OP).</span><span class="sxs-lookup"><span data-stu-id="8af75-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="8af75-112">O suporte de autenticação no Webassembly Blazor é criado sobre a biblioteca *oidc-Client. js* , que é usada para lidar com os detalhes do protocolo de autenticação subjacente.</span><span class="sxs-lookup"><span data-stu-id="8af75-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="8af75-113">Existem outras opções para autenticar SPAs, como o uso de cookies SameSite.</span><span class="sxs-lookup"><span data-stu-id="8af75-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="8af75-114">No entanto, o design de engenharia de Blazor Webassembly é liquidado em oAuth e OIDC como a melhor opção para autenticação em Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="8af75-114">However, the engineering design of Blazor WebAssembly is settled on oAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="8af75-115">[A autenticação baseada em token](xref:security/anti-request-forgery#token-based-authentication) baseada em [tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida pela [autenticação baseada em cookie](xref:security/anti-request-forgery#cookie-based-authentication) para motivos funcionais e de segurança:</span><span class="sxs-lookup"><span data-stu-id="8af75-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="8af75-116">O uso de um protocolo baseado em token oferece uma área de superfície de ataque menor, pois os tokens não são enviados em todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="8af75-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="8af75-117">Os pontos de extremidade do servidor não exigem proteção contra [falsificação de solicitação entre sites (CSRF)](xref:security/anti-request-forgery) porque os tokens são enviados explicitamente.</span><span class="sxs-lookup"><span data-stu-id="8af75-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="8af75-118">Isso permite que você hospede Blazor aplicativos Webassembly juntamente com os aplicativos MVC ou páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="8af75-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="8af75-119">Tokens têm permissões mais estreitas do que cookies.</span><span class="sxs-lookup"><span data-stu-id="8af75-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="8af75-120">Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.</span><span class="sxs-lookup"><span data-stu-id="8af75-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="8af75-121">Os tokens têm um tempo de vida curto, uma hora por padrão, o que limita a janela de ataque.</span><span class="sxs-lookup"><span data-stu-id="8af75-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="8af75-122">Os tokens também podem ser revogados a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="8af75-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="8af75-123">As JWTs independentes oferecem garantias ao cliente e ao servidor sobre o processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="8af75-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="8af75-124">Por exemplo, um cliente tem o meio de detectar e validar que os tokens que ele recebe são legítimos e foram emitidos como parte de um processo de autenticação específico.</span><span class="sxs-lookup"><span data-stu-id="8af75-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part a given authentication process.</span></span> <span data-ttu-id="8af75-125">Se um terceiro tentar alternar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.</span><span class="sxs-lookup"><span data-stu-id="8af75-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="8af75-126">Tokens com oAuth e OIDC não dependem do agente do usuário se comportarem corretamente para garantir que o aplicativo seja seguro.</span><span class="sxs-lookup"><span data-stu-id="8af75-126">Tokens with oAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="8af75-127">Protocolos baseados em token, como oAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.</span><span class="sxs-lookup"><span data-stu-id="8af75-127">Token-based protocols, such as oAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="8af75-128">Processo de autenticação com OIDC</span><span class="sxs-lookup"><span data-stu-id="8af75-128">Authentication process with OIDC</span></span>

<span data-ttu-id="8af75-129">A biblioteca de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` oferece vários primitivos para implementar a autenticação e a autorização usando o OIDC.</span><span class="sxs-lookup"><span data-stu-id="8af75-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="8af75-130">Em termos gerais, a autenticação funciona da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="8af75-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="8af75-131">Quando um usuário anônimo seleciona o botão de logon ou solicita uma página com o atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado, o usuário é redirecionado para a página de logon do aplicativo (`/authentication/login`).</span><span class="sxs-lookup"><span data-stu-id="8af75-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="8af75-132">Na página de logon, a biblioteca de autenticação se prepara para um redirecionamento para o ponto de extremidade de autorização.</span><span class="sxs-lookup"><span data-stu-id="8af75-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="8af75-133">O ponto de extremidade de autorização está fora do Blazor aplicativo Webassembly e pode ser hospedado em uma origem separada.</span><span class="sxs-lookup"><span data-stu-id="8af75-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="8af75-134">O ponto de extremidade é responsável por determinar se o usuário está autenticado e para emitir um ou mais tokens em resposta.</span><span class="sxs-lookup"><span data-stu-id="8af75-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="8af75-135">A biblioteca de autenticação fornece um retorno de chamada de logon para receber a resposta de autenticação.</span><span class="sxs-lookup"><span data-stu-id="8af75-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="8af75-136">Se o usuário não for autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente é ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="8af75-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="8af75-137">Se o usuário já foi autenticado, o ponto de extremidade de autorização gera os tokens apropriados e redireciona o navegador de volta para o ponto de extremidade de retorno de chamada de logon (`/authentication/login-callback`).</span><span class="sxs-lookup"><span data-stu-id="8af75-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="8af75-138">Quando o aplicativo Webassembly Blazor carrega o ponto de extremidade de retorno de chamada de logon (`/authentication/login-callback`), a resposta de autenticação é processada.</span><span class="sxs-lookup"><span data-stu-id="8af75-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="8af75-139">Se o processo de autenticação for concluído com êxito, o usuário será autenticado e, opcionalmente, enviado de volta para a URL protegida original solicitada pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="8af75-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="8af75-140">Se o processo de autenticação falhar por algum motivo, o usuário será enviado para a página de falha de logon (`/authentication/login-failed`) e um erro será exibido.</span><span class="sxs-lookup"><span data-stu-id="8af75-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>