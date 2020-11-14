---
title: :::no-loc(Blazor):::Autenticação e autorização do ASP.NET Core
author: guardrex
description: 'Saiba mais sobre os :::no-loc(Blazor)::: cenários de autenticação e autorização.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: 6435a7c9ce2a30873f0d3475a38270d3dea1b300
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595461"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a><span data-ttu-id="6b317-103">:::no-loc(Blazor):::Autenticação e autorização do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b317-103">ASP.NET Core :::no-loc(Blazor)::: authentication and authorization</span></span>

<span data-ttu-id="6b317-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6b317-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b317-105">O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em :::no-loc(Blazor)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b317-105">ASP.NET Core supports the configuration and management of security in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="6b317-106">Cenários de segurança diferem entre :::no-loc(Blazor Server)::: :::no-loc(Blazor WebAssembly)::: aplicativos e.</span><span class="sxs-lookup"><span data-stu-id="6b317-106">Security scenarios differ between :::no-loc(Blazor Server)::: and :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="6b317-107">Como os :::no-loc(Blazor Server)::: aplicativos são executados no servidor, as verificações de autorização são capazes de determinar:</span><span class="sxs-lookup"><span data-stu-id="6b317-107">Because :::no-loc(Blazor Server)::: apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="6b317-108">As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).</span><span class="sxs-lookup"><span data-stu-id="6b317-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="6b317-109">As regras de acesso para áreas do aplicativo e componentes.</span><span class="sxs-lookup"><span data-stu-id="6b317-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="6b317-110">:::no-loc(Blazor WebAssembly)::: os aplicativos são executados no cliente.</span><span class="sxs-lookup"><span data-stu-id="6b317-110">:::no-loc(Blazor WebAssembly)::: apps run on the client.</span></span> <span data-ttu-id="6b317-111">A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas.</span><span class="sxs-lookup"><span data-stu-id="6b317-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="6b317-112">Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um :::no-loc(Blazor WebAssembly)::: aplicativo não pode impor regras de acesso de autorização.</span><span class="sxs-lookup"><span data-stu-id="6b317-112">Since client-side checks can be modified or bypassed by a user, a :::no-loc(Blazor WebAssembly)::: app can't enforce authorization access rules.</span></span>

<span data-ttu-id="6b317-113">As [ :::no-loc(Razor)::: convenções de autorização de páginas](xref:security/authorization/razor-pages-authorization) não se aplicam a componentes roteáveis :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="6b317-113">[:::no-loc(Razor)::: Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable :::no-loc(Razor)::: components.</span></span> <span data-ttu-id="6b317-114">Se um componente não roteável :::no-loc(Razor)::: for [inserido em uma página](xref:blazor/components/prerendering-and-integration), as convenções de autorização da página afetarão indiretamente o :::no-loc(Razor)::: componente junto com o restante do conteúdo da página.</span><span class="sxs-lookup"><span data-stu-id="6b317-114">If a non-routable :::no-loc(Razor)::: component is [embedded in a page](xref:blazor/components/prerendering-and-integration), the page's authorization conventions indirectly affect the :::no-loc(Razor)::: component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="6b317-115"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601> e <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> não têm suporte em :::no-loc(Razor)::: componentes do.</span><span class="sxs-lookup"><span data-stu-id="6b317-115"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> aren't supported in :::no-loc(Razor)::: components.</span></span>

## <a name="authentication"></a><span data-ttu-id="6b317-116">Autenticação</span><span class="sxs-lookup"><span data-stu-id="6b317-116">Authentication</span></span>

<span data-ttu-id="6b317-117">:::no-loc(Blazor)::: usa os mecanismos de autenticação de ASP.NET Core existentes para estabelecer a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="6b317-117">:::no-loc(Blazor)::: uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="6b317-118">O mecanismo exato depende de como o :::no-loc(Blazor)::: aplicativo é hospedado :::no-loc(Blazor WebAssembly)::: ou :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="6b317-118">The exact mechanism depends on how the :::no-loc(Blazor)::: app is hosted, :::no-loc(Blazor WebAssembly)::: or :::no-loc(Blazor Server):::.</span></span>

### <a name="no-locblazor-webassembly-authentication"></a><span data-ttu-id="6b317-119">autenticação :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="6b317-119">:::no-loc(Blazor WebAssembly)::: authentication</span></span>

<span data-ttu-id="6b317-120">Em :::no-loc(Blazor WebAssembly)::: aplicativos, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários.</span><span class="sxs-lookup"><span data-stu-id="6b317-120">In :::no-loc(Blazor WebAssembly)::: apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="6b317-121">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="6b317-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="6b317-122">Adicione o seguinte:</span><span class="sxs-lookup"><span data-stu-id="6b317-122">Add the following:</span></span>

* <span data-ttu-id="6b317-123">Uma referência de pacote para [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6b317-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="6b317-124">O `Microsoft.AspNetCore.Components.Authorization` namespace para o arquivo do aplicativo `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="6b317-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="6b317-125">Para lidar com a autenticação, o uso de um serviço interno ou personalizado <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> é abordado nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="6b317-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="6b317-126">Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:blazor/security/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="6b317-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="no-locblazor-server-authentication"></a><span data-ttu-id="6b317-127">autenticação :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="6b317-127">:::no-loc(Blazor Server)::: authentication</span></span>

<span data-ttu-id="6b317-128">:::no-loc(Blazor Server)::: os aplicativos operam em uma conexão em tempo real que é criada usando o :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="6b317-128">:::no-loc(Blazor Server)::: apps operate over a real-time connection that's created using :::no-loc(SignalR):::.</span></span> <span data-ttu-id="6b317-129">A [autenticação em :::no-loc(SignalR)::: aplicativos baseados no](xref:signalr/authn-and-authz) é tratada quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="6b317-129">[Authentication in :::no-loc(SignalR):::-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="6b317-130">A autenticação pode ser baseada em um :::no-loc(cookie)::: ou algum outro token de portador.</span><span class="sxs-lookup"><span data-stu-id="6b317-130">Authentication can be based on a :::no-loc(cookie)::: or some other bearer token.</span></span>

<span data-ttu-id="6b317-131">O <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> serviço interno para :::no-loc(Blazor Server)::: aplicativos obtém dados de estado de autenticação do ASP.NET Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="6b317-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for :::no-loc(Blazor Server)::: apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="6b317-132">É assim que o estado de autenticação se integra aos mecanismos existentes de autenticação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6b317-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="6b317-133">Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:blazor/security/server/index> .</span><span class="sxs-lookup"><span data-stu-id="6b317-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="6b317-134">Serviço AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="6b317-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="6b317-135">O <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> é o serviço subjacente usado pelos componentes <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> e <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> para obter o estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="6b317-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="6b317-136">Normalmente, você não usa o <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> diretamente.</span><span class="sxs-lookup"><span data-stu-id="6b317-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="6b317-137">Use o [ `AuthorizeView` componente](#authorizeview-component) ou [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) abordagens descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="6b317-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="6b317-138">A principal desvantagem de usar o <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.</span><span class="sxs-lookup"><span data-stu-id="6b317-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="6b317-139">O serviço <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="6b317-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            _authMessage = $"{user.:::no-loc(Identity):::.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="6b317-140">Se `user.:::no-loc(Identity):::.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="6b317-140">If `user.:::no-loc(Identity):::.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="6b317-141">Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/fundamentals/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6b317-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="6b317-142">Implementar um AuthenticationStateProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="6b317-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="6b317-143">Se o aplicativo exigir um provedor personalizado, implemente <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> e substitua `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="6b317-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new Claims:::no-loc(Identity):::(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="6b317-144">Em um :::no-loc(Blazor WebAssembly)::: aplicativo, o `CustomAuthStateProvider` serviço é registrado em `Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="6b317-144">In a :::no-loc(Blazor WebAssembly)::: app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="6b317-145">Em um :::no-loc(Blazor Server)::: aplicativo, o `CustomAuthStateProvider` serviço é registrado em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6b317-145">In a :::no-loc(Blazor Server)::: app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="6b317-146">Usando o `CustomAuthStateProvider` no exemplo anterior, todos os usuários são autenticados com o nome de usuário `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="6b317-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="6b317-147">Expor o estado de autenticação como um parâmetro em cascata</span><span class="sxs-lookup"><span data-stu-id="6b317-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="6b317-148">Se os dados de estado de autenticação forem necessários para a lógica de procedimento, como ao executar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="6b317-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            _authMessage = $"{user.:::no-loc(Identity):::.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="6b317-149">Se `user.:::no-loc(Identity):::.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="6b317-149">If `user.:::no-loc(Identity):::.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="6b317-150">Configure o `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parâmetro em cascata usando os <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componentes e <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> no `App` componente ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="6b317-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="6b317-151">Em um :::no-loc(Blazor WebAssembly)::: aplicativo, adicione serviços para opções e autorização para `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="6b317-151">In a :::no-loc(Blazor WebAssembly)::: App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="6b317-152">Em um :::no-loc(Blazor Server)::: aplicativo, os serviços para opções e autorização já estão presentes, portanto, nenhuma ação adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="6b317-152">In a :::no-loc(Blazor Server)::: app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="6b317-153">Autorização</span><span class="sxs-lookup"><span data-stu-id="6b317-153">Authorization</span></span>

<span data-ttu-id="6b317-154">Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.</span><span class="sxs-lookup"><span data-stu-id="6b317-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="6b317-155">O acesso geralmente é concedido ou negado com base nos seguintes casos:</span><span class="sxs-lookup"><span data-stu-id="6b317-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="6b317-156">Se o usuário está autenticado (conectado).</span><span class="sxs-lookup"><span data-stu-id="6b317-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="6b317-157">Se o usuário está em uma *função*.</span><span class="sxs-lookup"><span data-stu-id="6b317-157">A user is in a *role*.</span></span>
* <span data-ttu-id="6b317-158">Se o usuário tem uma *declaração*.</span><span class="sxs-lookup"><span data-stu-id="6b317-158">A user has a *claim*.</span></span>
* <span data-ttu-id="6b317-159">Se uma *política* é atendida.</span><span class="sxs-lookup"><span data-stu-id="6b317-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="6b317-160">Cada um desses conceitos é o mesmo de um aplicativo ASP.NET Core MVC ou de :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="6b317-160">Each of these concepts is the same as in an ASP.NET Core MVC or :::no-loc(Razor)::: Pages app.</span></span> <span data-ttu-id="6b317-161">Para obter mais informações sobre ASP.NET Core segurança, consulte os artigos em [ASP.NET Core segurança :::no-loc(Identity)::: e ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="6b317-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and :::no-loc(Identity):::](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="6b317-162">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="6b317-162">AuthorizeView component</span></span>

<span data-ttu-id="6b317-163">O <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> componente exibe seletivamente o conteúdo da interface de usuário dependendo se o usuário está autorizado.</span><span class="sxs-lookup"><span data-stu-id="6b317-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI content depending on whether the user is authorized.</span></span> <span data-ttu-id="6b317-164">Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.</span><span class="sxs-lookup"><span data-stu-id="6b317-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="6b317-165">O componente expõe uma variável `context` do tipo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, que pode ser usada para acessar informações sobre o usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="6b317-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="6b317-166">Você também pode fornecer conteúdo diferente para exibição se o usuário não estiver autorizado:</span><span class="sxs-lookup"><span data-stu-id="6b317-166">You can also supply different content for display if the user isn't authorized:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

<span data-ttu-id="6b317-167">O conteúdo de `<Authorized>` `<NotAuthorized>` marcas e pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="6b317-167">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="6b317-168">Um manipulador de eventos padrão para um elemento autorizado, como o `SecureMethod` método para o `<button>` elemento no exemplo anterior, só pode ser invocado por um usuário autorizado.</span><span class="sxs-lookup"><span data-stu-id="6b317-168">A default event handler for an authorized element, such as the `SecureMethod` method for the `<button>` element in the preceding example, can only be invoked by an authorized user.</span></span>

<span data-ttu-id="6b317-169">As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).</span><span class="sxs-lookup"><span data-stu-id="6b317-169">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="6b317-170">Se as condições de autorização não forem especificadas, o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usará uma política padrão e tratará:</span><span class="sxs-lookup"><span data-stu-id="6b317-170">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="6b317-171">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="6b317-171">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="6b317-172">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="6b317-172">Unauthenticated (signed-out) users as unauthorized.</span></span>

<span data-ttu-id="6b317-173">O <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> componente pode ser usado no `NavMenu` componente ( `Shared/NavMenu.razor` ) para exibir um item de lista ( `<li>...</li>` ) para um [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) () <xref:Microsoft.AspNetCore.Components.Routing.NavLink> , mas observe que essa abordagem apenas remove o item de lista da saída renderizada.</span><span class="sxs-lookup"><span data-stu-id="6b317-173">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="6b317-174">Ele não impede que o usuário navegue até o componente.</span><span class="sxs-lookup"><span data-stu-id="6b317-174">It doesn't prevent the user from navigating to the component.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="6b317-175">Autorização baseada em funções e em políticas</span><span class="sxs-lookup"><span data-stu-id="6b317-175">Role-based and policy-based authorization</span></span>

<span data-ttu-id="6b317-176">O componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> dá suporte à autorização *baseada em funções* ou *baseada em políticas*.</span><span class="sxs-lookup"><span data-stu-id="6b317-176">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="6b317-177">Para a autorização baseada em funções, use o parâmetro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:</span><span class="sxs-lookup"><span data-stu-id="6b317-177">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="6b317-178">Para obter mais informações, consulte <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="6b317-178">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="6b317-179">Para a autorização baseada em políticas, use o parâmetro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:</span><span class="sxs-lookup"><span data-stu-id="6b317-179">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="6b317-180">A autorização baseada em declarações é um caso especial de autorização baseada em políticas.</span><span class="sxs-lookup"><span data-stu-id="6b317-180">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="6b317-181">Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração.</span><span class="sxs-lookup"><span data-stu-id="6b317-181">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="6b317-182">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6b317-182">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6b317-183">Essas APIs podem ser usadas em um :::no-loc(Blazor Server)::: ou em :::no-loc(Blazor WebAssembly)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b317-183">These APIs can be used in either :::no-loc(Blazor Server)::: or :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="6b317-184">Se <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> e <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> não forem especificados, o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usará a política padrão.</span><span class="sxs-lookup"><span data-stu-id="6b317-184">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="6b317-185">Conteúdo exibido durante a autenticação assíncrona</span><span class="sxs-lookup"><span data-stu-id="6b317-185">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="6b317-186">:::no-loc(Blazor)::: permite que o estado de autenticação seja determinado de *forma assíncrona*.</span><span class="sxs-lookup"><span data-stu-id="6b317-186">:::no-loc(Blazor)::: allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="6b317-187">O cenário principal para essa abordagem é em :::no-loc(Blazor WebAssembly)::: aplicativos que fazem uma solicitação para um ponto de extremidade externo para autenticação.</span><span class="sxs-lookup"><span data-stu-id="6b317-187">The primary scenario for this approach is in :::no-loc(Blazor WebAssembly)::: apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="6b317-188">Enquanto a autenticação estiver em andamento, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> não exibirá nenhum conteúdo por padrão.</span><span class="sxs-lookup"><span data-stu-id="6b317-188">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="6b317-189">Para exibir o conteúdo enquanto a autenticação ocorre, use a `<Authorizing>` marca:</span><span class="sxs-lookup"><span data-stu-id="6b317-189">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="6b317-190">Essa abordagem não é normalmente aplicável a :::no-loc(Blazor Server)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6b317-190">This approach isn't normally applicable to :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="6b317-191">:::no-loc(Blazor Server)::: os aplicativos conhecem o estado de autenticação assim que o estado é estabelecido.</span><span class="sxs-lookup"><span data-stu-id="6b317-191">:::no-loc(Blazor Server)::: apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="6b317-192"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> o conteúdo pode ser fornecido no :::no-loc(Blazor Server)::: componente de um aplicativo <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , mas o conteúdo nunca é exibido.</span><span class="sxs-lookup"><span data-stu-id="6b317-192"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a :::no-loc(Blazor Server)::: app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="6b317-193">Atributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="6b317-193">[Authorize] attribute</span></span>

<span data-ttu-id="6b317-194">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo pode ser usado em :::no-loc(Razor)::: componentes:</span><span class="sxs-lookup"><span data-stu-id="6b317-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="6b317-195">Somente [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o uso em `@page` componentes foi atingido por meio do :::no-loc(Blazor)::: roteador.</span><span class="sxs-lookup"><span data-stu-id="6b317-195">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the :::no-loc(Blazor)::: Router.</span></span> <span data-ttu-id="6b317-196">A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página.</span><span class="sxs-lookup"><span data-stu-id="6b317-196">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="6b317-197">Para autorizar a exibição de partes específicas dentro de uma página, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.</span><span class="sxs-lookup"><span data-stu-id="6b317-197">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="6b317-198">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo também oferece suporte à autorização baseada em função ou em políticas.</span><span class="sxs-lookup"><span data-stu-id="6b317-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="6b317-199">Para a autorização baseada em funções, use o parâmetro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:</span><span class="sxs-lookup"><span data-stu-id="6b317-199">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="6b317-200">Para a autorização baseada em políticas, use o parâmetro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:</span><span class="sxs-lookup"><span data-stu-id="6b317-200">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="6b317-201">Se nem <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nem <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> for especificado, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o usará a política padrão, que por padrão é tratar:</span><span class="sxs-lookup"><span data-stu-id="6b317-201">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="6b317-202">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="6b317-202">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="6b317-203">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="6b317-203">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="6b317-204">Personalizar conteúdo não autorizado com o componente Router</span><span class="sxs-lookup"><span data-stu-id="6b317-204">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="6b317-205">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, em conjunto com o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente, permite que o aplicativo especifique o conteúdo personalizado se:</span><span class="sxs-lookup"><span data-stu-id="6b317-205">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="6b317-206">O conteúdo não for encontrado.</span><span class="sxs-lookup"><span data-stu-id="6b317-206">Content isn't found.</span></span>
* <span data-ttu-id="6b317-207">O usuário falha [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) em uma condição aplicada ao componente.</span><span class="sxs-lookup"><span data-stu-id="6b317-207">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="6b317-208">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é abordado na seção [ `[Authorize]` atributo](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="6b317-208">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="6b317-209">A autenticação assíncrona estiver em andamento.</span><span class="sxs-lookup"><span data-stu-id="6b317-209">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="6b317-210">No modelo de :::no-loc(Blazor Server)::: projeto padrão, o `App` componente ( `App.razor` ) demonstra como definir o conteúdo personalizado:</span><span class="sxs-lookup"><span data-stu-id="6b317-210">In the default :::no-loc(Blazor Server)::: project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="6b317-211">O conteúdo das `<NotFound>` `<NotAuthorized>` marcas, e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="6b317-211">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="6b317-212">Se a `<NotAuthorized>` marca não for especificada, o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> usará a seguinte mensagem de fallback:</span><span class="sxs-lookup"><span data-stu-id="6b317-212">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="6b317-213">Notificação sobre mudanças no estado de autenticação</span><span class="sxs-lookup"><span data-stu-id="6b317-213">Notification about authentication state changes</span></span>

<span data-ttu-id="6b317-214">Se o aplicativo determinar que os dados de estado de autenticação subjacentes foram alterados (por exemplo, porque o usuário se desconectou ou outro usuário alterou suas funções), um [personalizado `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) poderá invocar opcionalmente o método <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> na <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> classe base.</span><span class="sxs-lookup"><span data-stu-id="6b317-214">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="6b317-215">Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) para realizar uma nova renderização usando os novos dados.</span><span class="sxs-lookup"><span data-stu-id="6b317-215">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="6b317-216">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="6b317-216">Procedural logic</span></span>

<span data-ttu-id="6b317-217">Se o aplicativo for necessário para verificar as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` para obter o usuário <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="6b317-217">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="6b317-218">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` pode ser combinado com outros serviços, como `IAuthorizationService` , para avaliar políticas.</span><span class="sxs-lookup"><span data-stu-id="6b317-218">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="6b317-219">Em um :::no-loc(Blazor WebAssembly)::: componente de aplicativo, adicione <xref:Microsoft.AspNetCore.Authorization> os <xref:Microsoft.AspNetCore.Components.Authorization> namespaces e:</span><span class="sxs-lookup"><span data-stu-id="6b317-219">In a :::no-loc(Blazor WebAssembly)::: app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="6b317-220">Esses namespaces podem ser fornecidos globalmente adicionando-os ao arquivo do aplicativo `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="6b317-220">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="6b317-221">Solucionar problemas de erros</span><span class="sxs-lookup"><span data-stu-id="6b317-221">Troubleshoot errors</span></span>

<span data-ttu-id="6b317-222">Erros comuns:</span><span class="sxs-lookup"><span data-stu-id="6b317-222">Common errors:</span></span>

* <span data-ttu-id="6b317-223">**A autorização requer um parâmetro em cascata do tipo `Task\<AuthenticationState>` . Considere usar `CascadingAuthenticationState` para fornecer isso.**</span><span class="sxs-lookup"><span data-stu-id="6b317-223">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="6b317-224">**`null` o valor é recebido para `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="6b317-224">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="6b317-225">É provável que o projeto não tenha sido criado usando um :::no-loc(Blazor Server)::: modelo com autenticação habilitada.</span><span class="sxs-lookup"><span data-stu-id="6b317-225">It's likely that the project wasn't created using a :::no-loc(Blazor Server)::: template with authentication enabled.</span></span> <span data-ttu-id="6b317-226">Empacote uma `<CascadingAuthenticationState>` parte da árvore de interface do usuário, por exemplo, no `App` componente ( `App.razor` ) da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6b317-226">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="6b317-227">O <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> fornece o `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parâmetro em cascata, que, por sua vez, recebe do <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> serviço de injeção subjacente.</span><span class="sxs-lookup"><span data-stu-id="6b317-227">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b317-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6b317-228">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="6b317-229">[Incrível :::no-loc(Blazor)::: :](https://github.com/AdrienTorris/awesome-blazor#authentication) links de exemplo da comunidade de autenticação</span><span class="sxs-lookup"><span data-stu-id="6b317-229">[Awesome :::no-loc(Blazor):::: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
