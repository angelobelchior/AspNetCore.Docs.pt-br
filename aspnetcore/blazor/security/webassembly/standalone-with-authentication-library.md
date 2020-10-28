---
title: 'Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core autônomo com a biblioteca de autenticação'
author: guardrex
description: 'Saiba como proteger um aplicativo ASP.NET Core :::no-loc(Blazor WebAssembly)::: autônomo com a biblioteca de autenticação.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 332bf73d21dfe36d2f79e4c016f7f8391c67a1f7
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690361"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="ab01e-103">Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core autônomo com a biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="ab01e-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with the Authentication library</span></span>

<span data-ttu-id="ab01e-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ab01e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ab01e-105">*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*</span><span class="sxs-lookup"><span data-stu-id="ab01e-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="ab01e-106">Para criar um [ :::no-loc(Blazor WebAssembly)::: aplicativo autônomo](xref:blazor/hosting-models#blazor-webassembly) que usa [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a biblioteca do, siga as orientações para sua escolha de ferramentas.</span><span class="sxs-lookup"><span data-stu-id="ab01e-106">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab01e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab01e-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ab01e-108">Para criar um novo :::no-loc(Blazor WebAssembly)::: projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="ab01e-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="ab01e-109">Depois de escolher o modelo de **:::no-loc(Blazor WebAssembly)::: aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="ab01e-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

1. <span data-ttu-id="ab01e-110">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="ab01e-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="ab01e-111">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ab01e-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="ab01e-112">Crie um novo :::no-loc(Blazor WebAssembly)::: projeto com um mecanismo de autenticação em uma pasta vazia.</span><span class="sxs-lookup"><span data-stu-id="ab01e-112">Create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="ab01e-113">Especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [:::no-loc(Identity):::](xref:security/authentication/identity) sistema ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ab01e-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="ab01e-114">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="ab01e-114">Placeholder</span></span>  | <span data-ttu-id="ab01e-115">Exemplo</span><span class="sxs-lookup"><span data-stu-id="ab01e-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="ab01e-116">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab01e-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="ab01e-117">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab01e-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab01e-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ab01e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ab01e-119">Para criar um novo :::no-loc(Blazor WebAssembly)::: projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="ab01e-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="ab01e-120">Na etapa **configurar seu novo :::no-loc(Blazor WebAssembly)::: aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="ab01e-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ab01e-121">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="ab01e-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="ab01e-122">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="ab01e-122">Authentication package</span></span>

<span data-ttu-id="ab01e-123">Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab01e-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="ab01e-124">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="ab01e-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ab01e-125">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ab01e-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="ab01e-126">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="ab01e-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ab01e-127">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="ab01e-127">Authentication service support</span></span>

<span data-ttu-id="ab01e-128">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método de extensão fornecido pelo [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote.</span><span class="sxs-lookup"><span data-stu-id="ab01e-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="ab01e-129">Esse método configura os serviços necessários para que o aplicativo interaja com o :::no-loc(Identity)::: provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="ab01e-129">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="ab01e-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="ab01e-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="ab01e-131">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="ab01e-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="ab01e-132">O suporte de autenticação para aplicativos autônomos é oferecido usando o OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="ab01e-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="ab01e-133">O <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando o OIDC.</span><span class="sxs-lookup"><span data-stu-id="ab01e-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="ab01e-134">Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC.</span><span class="sxs-lookup"><span data-stu-id="ab01e-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="ab01e-135">Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.</span><span class="sxs-lookup"><span data-stu-id="ab01e-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="ab01e-136">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="ab01e-136">Access token scopes</span></span>

<span data-ttu-id="ab01e-137">O :::no-loc(Blazor WebAssembly)::: modelo configura automaticamente os escopos padrão para `openid` e `profile` .</span><span class="sxs-lookup"><span data-stu-id="ab01e-137">The :::no-loc(Blazor WebAssembly)::: template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="ab01e-138">O :::no-loc(Blazor WebAssembly)::: modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="ab01e-138">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ab01e-139">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token padrão do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="ab01e-139">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="ab01e-140">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="ab01e-140">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ab01e-141">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="ab01e-141">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ab01e-142">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="ab01e-142">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="ab01e-143">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="ab01e-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ab01e-144">Página de índice</span><span class="sxs-lookup"><span data-stu-id="ab01e-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="ab01e-145">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ab01e-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ab01e-146">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="ab01e-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ab01e-147">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="ab01e-147">LoginDisplay component</span></span>

<span data-ttu-id="ab01e-148">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="ab01e-148">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="ab01e-149">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="ab01e-149">For authenticated users:</span></span>
  * <span data-ttu-id="ab01e-150">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="ab01e-150">Displays the current username.</span></span>
  * <span data-ttu-id="ab01e-151">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab01e-151">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="ab01e-152">Para usuários anônimos, o oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="ab01e-152">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.:::no-loc(Identity):::.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="ab01e-153">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="ab01e-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ab01e-154">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ab01e-154">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="ab01e-155">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="ab01e-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
