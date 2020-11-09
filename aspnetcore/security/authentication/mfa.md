---
title: Autenticação multifator no ASP.NET Core
author: damienbod
description: Saiba como configurar a MFA (autenticação multifator) em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060385"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="82385-103">Autenticação multifator no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82385-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="82385-104">Por [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="82385-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="82385-105">Exibir ou baixar o código de exemplo (repositório GitHub damienbod/AspNetCoreHybridFlowWithApi)</span><span class="sxs-lookup"><span data-stu-id="82385-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="82385-106">A autenticação multifator (MFA) é um processo no qual um usuário é solicitado durante um evento de entrada para formulários adicionais de identificação.</span><span class="sxs-lookup"><span data-stu-id="82385-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="82385-107">Esse prompt pode ser para inserir um código de um celular, usar uma chave FIDO2 ou para fornecer uma verificação de impressão digital.</span><span class="sxs-lookup"><span data-stu-id="82385-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="82385-108">Quando você precisa de uma segunda forma de autenticação, a segurança é aprimorada.</span><span class="sxs-lookup"><span data-stu-id="82385-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="82385-109">O fator adicional não é facilmente obtido ou duplicado por um invasor.</span><span class="sxs-lookup"><span data-stu-id="82385-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="82385-110">Este artigo aborda as seguintes áreas:</span><span class="sxs-lookup"><span data-stu-id="82385-110">This article covers the following areas:</span></span>

* <span data-ttu-id="82385-111">O que é MFA e quais fluxos de MFA são recomendados</span><span class="sxs-lookup"><span data-stu-id="82385-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="82385-112">Configurar o MFA para páginas de administração usando ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="82385-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="82385-113">Enviar requisito de entrada MFA ao servidor OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="82385-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="82385-114">Forçar ASP.NET Core cliente do OpenID Connect a exigir MFA</span><span class="sxs-lookup"><span data-stu-id="82385-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="82385-115">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="82385-115">MFA, 2FA</span></span>

<span data-ttu-id="82385-116">A MFA exige pelo menos dois ou mais tipos de provas para uma identidade como algo que você conhece, algo que você possui ou validação biométrica para o usuário se autenticar.</span><span class="sxs-lookup"><span data-stu-id="82385-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="82385-117">A autenticação de dois fatores (2FA) é como um subconjunto de MFA, mas a diferença é que a MFA pode exigir dois ou mais fatores para provar a identidade.</span><span class="sxs-lookup"><span data-stu-id="82385-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="82385-118">MFA TOTP (algoritmo de senha de uso único baseado em tempo)</span><span class="sxs-lookup"><span data-stu-id="82385-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="82385-119">O MFA usando TOTP é uma implementação com suporte usando ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="82385-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="82385-120">Isso pode ser usado junto com qualquer aplicativo autenticador compatível, incluindo:</span><span class="sxs-lookup"><span data-stu-id="82385-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="82385-121">Aplicativo Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="82385-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="82385-122">Aplicativo de autenticador do Google</span><span class="sxs-lookup"><span data-stu-id="82385-122">Google Authenticator App</span></span>

<span data-ttu-id="82385-123">Consulte o link a seguir para obter detalhes de implementação:</span><span class="sxs-lookup"><span data-stu-id="82385-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="82385-124">Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82385-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="82385-125">MFA FIDO2 ou com senha</span><span class="sxs-lookup"><span data-stu-id="82385-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="82385-126">No momento, FIDO2 é:</span><span class="sxs-lookup"><span data-stu-id="82385-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="82385-127">A maneira mais segura de obter MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="82385-128">O único fluxo de MFA que protege contra ataques de phishing.</span><span class="sxs-lookup"><span data-stu-id="82385-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="82385-129">No momento, ASP.NET Core não dá suporte diretamente a FIDO2.</span><span class="sxs-lookup"><span data-stu-id="82385-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="82385-130">FIDO2 pode ser usado para fluxos MFA ou com senha.</span><span class="sxs-lookup"><span data-stu-id="82385-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="82385-131">Azure Active Directory fornece suporte para fluxos FIDO2 e sem senha.</span><span class="sxs-lookup"><span data-stu-id="82385-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="82385-132">Para obter mais informações, consulte [Opções de autenticação com senha para Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="82385-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="82385-133">SMS MFA</span><span class="sxs-lookup"><span data-stu-id="82385-133">MFA SMS</span></span>

<span data-ttu-id="82385-134">A MFA com o SMS aumenta a segurança em grande comparação com a autenticação de senha (fator único).</span><span class="sxs-lookup"><span data-stu-id="82385-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="82385-135">No entanto, o uso do SMS como um segundo fator não é mais recomendado.</span><span class="sxs-lookup"><span data-stu-id="82385-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="82385-136">Existem muitos vetores de ataque conhecidos para esse tipo de implementação.</span><span class="sxs-lookup"><span data-stu-id="82385-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="82385-137">Diretrizes do NIST</span><span class="sxs-lookup"><span data-stu-id="82385-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="82385-138">Configurar o MFA para páginas de administração usando ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="82385-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="82385-139">A MFA pode ser forçada em usuários a acessarem páginas confidenciais em um ASP.NET Core Identity aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82385-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="82385-140">Isso pode ser útil para aplicativos em que existem diferentes níveis de acesso para as diferentes identidades.</span><span class="sxs-lookup"><span data-stu-id="82385-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="82385-141">Por exemplo, os usuários podem ser capazes de exibir os dados de perfil usando um logon de senha, mas um administrador precisaria usar o MFA para acessar as páginas administrativas.</span><span class="sxs-lookup"><span data-stu-id="82385-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="82385-142">Estender o logon com uma declaração MFA</span><span class="sxs-lookup"><span data-stu-id="82385-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="82385-143">O código de demonstração é configurado usando ASP.NET Core Identity e Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="82385-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="82385-144">O `AddIdentity` método é usado em vez de `AddDefaultIdentity` um, portanto, uma `IUserClaimsPrincipalFactory` implementação pode ser usada para adicionar declarações à identidade após um logon bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="82385-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="82385-145">A `AdditionalUserClaimsPrincipalFactory` classe adiciona a `amr` declaração às declarações do usuário somente após um logon bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="82385-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="82385-146">O valor da declaração é lido no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="82385-146">The claim's value is read from the database.</span></span> <span data-ttu-id="82385-147">A declaração é adicionada aqui porque o usuário só deve acessar a exibição protegida mais alta se a identidade tiver feito logon com MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="82385-148">Se a exibição do banco de dados for lida diretamente do banco de dados em vez de usar a declaração, é possível acessar a exibição sem MFA diretamente após ativar a MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="82385-149">Como a Identity configuração do serviço foi alterada na `Startup` classe, os layouts do Identity precisam ser atualizados.</span><span class="sxs-lookup"><span data-stu-id="82385-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="82385-150">Scaffold as Identity páginas no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82385-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="82385-151">Defina o layout no arquivo *Identity /Account/Manage/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="82385-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="82385-152">Também atribua o layout para todas as páginas de gerenciamento das Identity páginas:</span><span class="sxs-lookup"><span data-stu-id="82385-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="82385-153">Validar o requisito de MFA na página de administração</span><span class="sxs-lookup"><span data-stu-id="82385-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="82385-154">A Razor página Administração valida que o usuário fez logon usando MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="82385-155">No `OnGet` método, a identidade é usada para acessar as declarações do usuário.</span><span class="sxs-lookup"><span data-stu-id="82385-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="82385-156">A `amr` declaração é verificada quanto ao valor `mfa` .</span><span class="sxs-lookup"><span data-stu-id="82385-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="82385-157">Se a identidade não tiver essa declaração ou for `false` , a página será redirecionada para a página habilitar MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="82385-158">Isso é possível porque o usuário já fez logon, mas sem MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-158">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="82385-159">Lógica da interface do usuário para ativar ou desativar informações de logon</span><span class="sxs-lookup"><span data-stu-id="82385-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="82385-160">Uma política de autorização foi adicionada na inicialização.</span><span class="sxs-lookup"><span data-stu-id="82385-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="82385-161">A política requer a `amr` declaração com o valor `mfa` .</span><span class="sxs-lookup"><span data-stu-id="82385-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="82385-162">Essa política pode ser usada na `_Layout` exibição para mostrar ou ocultar o menu **admin** com o aviso:</span><span class="sxs-lookup"><span data-stu-id="82385-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="82385-163">Se a identidade tiver feito logon usando o MFA, o menu **admin** será exibido sem o aviso da dica de ferramenta.</span><span class="sxs-lookup"><span data-stu-id="82385-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="82385-164">Quando o usuário fizer logon sem MFA, o menu **admin (não habilitado)** será exibido junto com a dica de ferramenta que informa ao usuário (explicando o aviso).</span><span class="sxs-lookup"><span data-stu-id="82385-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="82385-165">Se o usuário fizer logon sem MFA, o aviso será exibido:</span><span class="sxs-lookup"><span data-stu-id="82385-165">If the user logs in without MFA, the warning is displayed:</span></span>

![Autenticação de MFA do administrador](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="82385-167">O usuário é redirecionado para a exibição habilitar do MFA ao clicar no link do **administrador** :</span><span class="sxs-lookup"><span data-stu-id="82385-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![O administrador ativa a autenticação MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="82385-169">Enviar requisito de entrada MFA ao servidor OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="82385-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="82385-170">O `acr_values` parâmetro pode ser usado para passar o `mfa` valor necessário do cliente para o servidor em uma solicitação de autenticação.</span><span class="sxs-lookup"><span data-stu-id="82385-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="82385-171">O `acr_values` parâmetro precisa ser manipulado no servidor OpenID Connect para que isso funcione.</span><span class="sxs-lookup"><span data-stu-id="82385-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="82385-172">Cliente do OpenID Connect ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82385-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="82385-173">O Razor aplicativo cliente do OpenID Connect de páginas ASP.NET Core usa o `AddOpenIdConnect` método para fazer logon no servidor do OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="82385-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="82385-174">O `acr_values` parâmetro é definido com o `mfa` valor e enviado com a solicitação de autenticação.</span><span class="sxs-lookup"><span data-stu-id="82385-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="82385-175">O `OpenIdConnectEvents` é usado para adicionar isso.</span><span class="sxs-lookup"><span data-stu-id="82385-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="82385-176">Para obter `acr_values` os valores de parâmetro recomendados, consulte [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="82385-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="82385-177">Exemplo Identity de servidor OpenID Connect Server 4 com ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="82385-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="82385-178">No servidor OpenID Connect, que é implementado usando ASP.NET Core Identity com exibições do MVC, uma nova exibição chamada *ErrorEnable2FA. cshtml* é criada.</span><span class="sxs-lookup"><span data-stu-id="82385-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="82385-179">A exibição:</span><span class="sxs-lookup"><span data-stu-id="82385-179">The view:</span></span>

* <span data-ttu-id="82385-180">Exibe se o Identity é proveniente de um aplicativo que requer MFA, mas o usuário não ativou isso no Identity .</span><span class="sxs-lookup"><span data-stu-id="82385-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="82385-181">Informa ao usuário e adiciona um link para ativar isso.</span><span class="sxs-lookup"><span data-stu-id="82385-181">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="82385-182">No `Login` método, a `IIdentityServerInteractionService` implementação da interface `_interaction` é usada para acessar os parâmetros de solicitação do OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="82385-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="82385-183">O `acr_values` parâmetro é acessado usando a `AcrValues` propriedade.</span><span class="sxs-lookup"><span data-stu-id="82385-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="82385-184">Como o cliente enviou isso com `mfa` set, ele pode ser verificado.</span><span class="sxs-lookup"><span data-stu-id="82385-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="82385-185">Se a MFA for necessária e o usuário no ASP.NET Core Identity tiver a MFA habilitada, o logon continuará.</span><span class="sxs-lookup"><span data-stu-id="82385-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="82385-186">Quando o usuário não tem a MFA habilitada, o usuário é redirecionado para a exibição personalizada *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="82385-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="82385-187">Em seguida, ASP.NET Core Identity o assina o usuário.</span><span class="sxs-lookup"><span data-stu-id="82385-187">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="82385-188">O `ExternalLoginCallback` método funciona como o Identity logon local.</span><span class="sxs-lookup"><span data-stu-id="82385-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="82385-189">A `AcrValues` propriedade é verificada quanto ao `mfa` valor.</span><span class="sxs-lookup"><span data-stu-id="82385-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="82385-190">Se o `mfa` valor estiver presente, a MFA será forçada antes que o logon seja concluído (por exemplo, Redirecionado para a `ErrorEnable2FA` exibição).</span><span class="sxs-lookup"><span data-stu-id="82385-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="82385-191">Se o usuário já estiver conectado, o aplicativo cliente:</span><span class="sxs-lookup"><span data-stu-id="82385-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="82385-192">Ainda valida a `amr` declaração.</span><span class="sxs-lookup"><span data-stu-id="82385-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="82385-193">Pode configurar a MFA com um link para a ASP.NET Core Identity exibição.</span><span class="sxs-lookup"><span data-stu-id="82385-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="82385-195">Forçar ASP.NET Core cliente do OpenID Connect a exigir MFA</span><span class="sxs-lookup"><span data-stu-id="82385-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="82385-196">Este exemplo mostra como um Razor aplicativo de página ASP.NET Core, que usa o OpenID Connect para entrar, pode exigir que os usuários tenham se autenticado usando o MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="82385-197">Para validar o requisito de MFA, um `IAuthorizationRequirement` requisito é criado.</span><span class="sxs-lookup"><span data-stu-id="82385-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="82385-198">Isso será adicionado às páginas usando uma política que requer MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="82385-199">Um `AuthorizationHandler` é implementado, que usará a `amr` declaração e verificará o valor `mfa` .</span><span class="sxs-lookup"><span data-stu-id="82385-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="82385-200">O `amr` é retornado no `id_token` de uma autenticação bem-sucedida e pode ter muitos valores diferentes, conforme definido na especificação de [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="82385-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="82385-201">O valor retornado depende de como a identidade é autenticada e na implementação do servidor OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="82385-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="82385-202">O `AuthorizationHandler` usa o `RequireMfa` requisito e valida a `amr` declaração.</span><span class="sxs-lookup"><span data-stu-id="82385-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="82385-203">O servidor OpenID Connect pode ser implementado usando o Identity Server4 com ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="82385-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="82385-204">Quando um usuário faz logon usando TOTP, a `amr` declaração é retornada com um valor de MFA.</span><span class="sxs-lookup"><span data-stu-id="82385-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="82385-205">Se estiver usando uma implementação de servidor OpenID Connect diferente ou um tipo de MFA diferente, a `amr` declaração será ou poderá ter um valor diferente.</span><span class="sxs-lookup"><span data-stu-id="82385-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="82385-206">O código deve ser estendido para aceitar isso também.</span><span class="sxs-lookup"><span data-stu-id="82385-206">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="82385-207">No `Startup.ConfigureServices` método, o `AddOpenIdConnect` método é usado como o esquema de desafio padrão.</span><span class="sxs-lookup"><span data-stu-id="82385-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="82385-208">O manipulador de autorização, que é usado para verificar a `amr` declaração, é adicionado à inversão do contêiner de controle.</span><span class="sxs-lookup"><span data-stu-id="82385-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="82385-209">Em seguida, uma política é criada, o que adiciona o `RequireMfa` requisito.</span><span class="sxs-lookup"><span data-stu-id="82385-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="82385-210">Essa política é usada na Razor página conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="82385-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="82385-211">A política também pode ser adicionada globalmente para todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82385-211">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="82385-212">Se o usuário se autenticar sem MFA, a `amr` declaração provavelmente terá um `pwd` valor.</span><span class="sxs-lookup"><span data-stu-id="82385-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="82385-213">A solicitação não será autorizada a acessar a página.</span><span class="sxs-lookup"><span data-stu-id="82385-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="82385-214">Usando os valores padrão, o usuário será redirecionado para a página *Account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="82385-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="82385-215">Esse comportamento pode ser alterado ou você pode implementar sua própria lógica personalizada aqui.</span><span class="sxs-lookup"><span data-stu-id="82385-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="82385-216">Neste exemplo, um link é adicionado para que o usuário válido possa configurar o MFA para sua conta.</span><span class="sxs-lookup"><span data-stu-id="82385-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="82385-217">Agora, somente os usuários que se autenticam com o MFA podem acessar a página ou o site.</span><span class="sxs-lookup"><span data-stu-id="82385-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="82385-218">Se tipos de MFA diferentes forem usados ou se 2FA estiver ok, a `amr` declaração terá valores diferentes e precisará ser processada corretamente.</span><span class="sxs-lookup"><span data-stu-id="82385-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="82385-219">Servidores OpenID Connect diferentes também retornam valores diferentes para essa declaração e podem não seguir a especificação de [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="82385-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="82385-220">Ao fazer logon sem MFA (por exemplo, usando apenas uma senha):</span><span class="sxs-lookup"><span data-stu-id="82385-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="82385-221">O `amr` tem o `pwd` valor:</span><span class="sxs-lookup"><span data-stu-id="82385-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="82385-223">Acesso negado:</span><span class="sxs-lookup"><span data-stu-id="82385-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="82385-225">Como alternativa, faça logon usando OTP com Identity :</span><span class="sxs-lookup"><span data-stu-id="82385-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="82385-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="82385-227">Additional resources</span></span>

* [<span data-ttu-id="82385-228">Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82385-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="82385-229">Opções de autenticação com senha para Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="82385-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="82385-230">FIDO2 biblioteca .NET para FIDO2/webauthn atestado e asserção usando o .NET</span><span class="sxs-lookup"><span data-stu-id="82385-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="82385-231">Webauthn awesome</span><span class="sxs-lookup"><span data-stu-id="82385-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
