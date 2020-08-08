---
title: Autenticação do Facebook, do Google e do provedor externo sem ASP.NET CoreIdentity
author: rick-anderson
description: Uma explicação de como usar o Facebook, o Google, o Twitter, etc. a autenticação de usuário da conta sem ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 73055a262ac69c0fd6a7f59e77d23121e71ea3dd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021660"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-no-locidentity"></a><span data-ttu-id="7e9af-103">Usar autenticação de provedor de entrada social sem ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="7e9af-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="7e9af-104">Por [Kirk Larkin](https://twitter.com/serpent5) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7e9af-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7e9af-105"><xref:security/authentication/social/index>Descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="7e9af-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="7e9af-106">A abordagem descrita nesse tópico inclui ASP.NET Core Identity como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7e9af-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="7e9af-107">Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7e9af-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="7e9af-108">Isso é útil para aplicativos que não exigem todos os recursos do ASP.NET Core Identity , mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="7e9af-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="7e9af-109">Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="7e9af-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="7e9af-110">Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google.</span><span class="sxs-lookup"><span data-stu-id="7e9af-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="7e9af-111">Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="7e9af-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="7e9af-112">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="7e9af-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7e9af-113">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="7e9af-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7e9af-114">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="7e9af-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7e9af-115">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="7e9af-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="7e9af-116">Configuração</span><span class="sxs-lookup"><span data-stu-id="7e9af-116">Configuration</span></span>

<span data-ttu-id="7e9af-117">No `ConfigureServices` método, configure os esquemas de autenticação do aplicativo com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos, e <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="7e9af-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="7e9af-118">A chamada para <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> define o aplicativo <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="7e9af-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="7e9af-119">O `DefaultScheme` é o esquema padrão usado pelos seguintes `HttpContext` métodos de extensão de autenticação:</span><span class="sxs-lookup"><span data-stu-id="7e9af-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="7e9af-120">Definir o aplicativo `DefaultScheme` como [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") configura o aplicativo para usar Cookie s como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="7e9af-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="7e9af-121">Definir o aplicativo <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="7e9af-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="7e9af-122">`DefaultChallengeScheme`substituições `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="7e9af-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="7e9af-123">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.</span><span class="sxs-lookup"><span data-stu-id="7e9af-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="7e9af-124">No `Startup.Configure` , chame `UseAuthentication` e `UseAuthorization` entre chamar `UseRouting` e `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="7e9af-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="7e9af-125">Isso define a `HttpContext.User` propriedade e executa o middleware de autorização para solicitações:</span><span class="sxs-lookup"><span data-stu-id="7e9af-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="7e9af-126">Para saber mais sobre esquemas de autenticação, consulte [conceitos de autenticação](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="7e9af-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="7e9af-127">Para saber mais sobre cookie autenticação, consulte <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="7e9af-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="7e9af-128">Aplicar autorização</span><span class="sxs-lookup"><span data-stu-id="7e9af-128">Apply authorization</span></span>

<span data-ttu-id="7e9af-129">Teste a configuração de autenticação do aplicativo aplicando o `AuthorizeAttribute` atributo a um controlador, uma ação ou uma página.</span><span class="sxs-lookup"><span data-stu-id="7e9af-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="7e9af-130">O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:</span><span class="sxs-lookup"><span data-stu-id="7e9af-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="7e9af-131">Sair</span><span class="sxs-lookup"><span data-stu-id="7e9af-131">Sign out</span></span>

<span data-ttu-id="7e9af-132">Para desconectar o usuário atual e excluir seus cookie , chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="7e9af-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="7e9af-133">O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :</span><span class="sxs-lookup"><span data-stu-id="7e9af-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="7e9af-134">Observe que a chamada para não `SignOutAsync` especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7e9af-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="7e9af-135">O aplicativo `DefaultScheme` do `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.</span><span class="sxs-lookup"><span data-stu-id="7e9af-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e9af-136">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7e9af-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e9af-137"><xref:security/authentication/social/index>Descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="7e9af-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="7e9af-138">A abordagem descrita nesse tópico inclui ASP.NET Core Identity como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7e9af-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="7e9af-139">Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7e9af-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="7e9af-140">Isso é útil para aplicativos que não exigem todos os recursos do ASP.NET Core Identity , mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="7e9af-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="7e9af-141">Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="7e9af-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="7e9af-142">Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google.</span><span class="sxs-lookup"><span data-stu-id="7e9af-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="7e9af-143">Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="7e9af-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="7e9af-144">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="7e9af-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7e9af-145">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="7e9af-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7e9af-146">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="7e9af-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7e9af-147">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="7e9af-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="7e9af-148">Configuração</span><span class="sxs-lookup"><span data-stu-id="7e9af-148">Configuration</span></span>

<span data-ttu-id="7e9af-149">No `ConfigureServices` método, configure os esquemas de autenticação do aplicativo com os `AddAuthentication` `AddCookie` métodos, e `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="7e9af-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="7e9af-150">A chamada para [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) define o [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7e9af-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="7e9af-151">O `DefaultScheme` é o esquema padrão usado pelos seguintes `HttpContext` métodos de extensão de autenticação:</span><span class="sxs-lookup"><span data-stu-id="7e9af-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="7e9af-152">Definir o aplicativo `DefaultScheme` como [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") configura o aplicativo para usar Cookie s como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="7e9af-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="7e9af-153">Definir o aplicativo <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="7e9af-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="7e9af-154">`DefaultChallengeScheme`substituições `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="7e9af-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="7e9af-155">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.</span><span class="sxs-lookup"><span data-stu-id="7e9af-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="7e9af-156">No `Configure` método, chame o `UseAuthentication` método para invocar o middleware de autenticação que define a `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="7e9af-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="7e9af-157">Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="7e9af-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="7e9af-158">Para saber mais sobre esquemas de autenticação, consulte [conceitos de autenticação](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="7e9af-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="7e9af-159">Para saber mais sobre cookie autenticação, consulte <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="7e9af-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="7e9af-160">Aplicar autorização</span><span class="sxs-lookup"><span data-stu-id="7e9af-160">Apply authorization</span></span>

<span data-ttu-id="7e9af-161">Teste a configuração de autenticação do aplicativo aplicando o `AuthorizeAttribute` atributo a um controlador, uma ação ou uma página.</span><span class="sxs-lookup"><span data-stu-id="7e9af-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="7e9af-162">O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:</span><span class="sxs-lookup"><span data-stu-id="7e9af-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="7e9af-163">Sair</span><span class="sxs-lookup"><span data-stu-id="7e9af-163">Sign out</span></span>

<span data-ttu-id="7e9af-164">Para desconectar o usuário atual e excluir seus cookie , chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="7e9af-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="7e9af-165">O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :</span><span class="sxs-lookup"><span data-stu-id="7e9af-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="7e9af-166">Observe que a chamada para não `SignOutAsync` especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7e9af-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="7e9af-167">O aplicativo `DefaultScheme` do `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.</span><span class="sxs-lookup"><span data-stu-id="7e9af-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e9af-168">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7e9af-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
