---
title: ASP.NET Core Blazor Server cenários de segurança adicionais
author: guardrex
description: Saiba como configurar o Blazor Server para cenários de segurança adicionais.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- appsettings.json
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: ac30b2ba9da4b5dbc2e02a2f6eb1252927483f73
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055497"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server cenários de segurança adicionais

Por [Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Passar tokens para um Blazor Server aplicativo</h2>

Os tokens disponíveis fora dos Razor componentes em um Blazor Server aplicativo podem ser passados para os componentes com a abordagem descrita nesta seção.

Autentique o Blazor Server aplicativo como você faria com páginas regulares Razor ou um aplicativo MVC. Provisione e salve os tokens na autenticação cookie . Por exemplo:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Opcionalmente, escopos adicionais são adicionados com `options.Scope.Add("{SCOPE}");` , em que o espaço reservado `{SCOPE}` é o escopo adicional a ser adicionado.

Defina um serviço de provedor de token com **escopo** que possa ser usado no Blazor aplicativo para resolver os tokens da [injeção de dependência (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

No `Startup.ConfigureServices` , adicione serviços para:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Defina uma classe para passar o estado inicial do aplicativo com os tokens de acesso e de atualização:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

No `_Host.cshtml` arquivo, crie e instância do `InitialApplicationState` e passe-o como um parâmetro para o aplicativo:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

No `App` componente ( `App.razor` ), resolva o serviço e inicialize-o com os dados do parâmetro:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Adicione uma referência de pacote ao aplicativo para o [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pacote NuGet.

No serviço que faz uma solicitação de API segura, insira o provedor de token e recupere o token para a solicitação de API:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Definir o esquema de autenticação</h2>

Para um aplicativo que usa mais de um middleware de autenticação e, portanto, tem mais de um esquema de autenticação, o esquema que o Blazor usa pode ser definido explicitamente na configuração do ponto de extremidade de `Startup.Configure` . O exemplo a seguir define o esquema de Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Passar tokens para um Blazor Server aplicativo</h2>

Os tokens disponíveis fora dos Razor componentes em um Blazor Server aplicativo podem ser passados para os componentes com a abordagem descrita nesta seção.

Autentique o Blazor Server aplicativo como você faria com páginas regulares Razor ou um aplicativo MVC. Provisione e salve os tokens na autenticação cookie . Por exemplo:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Opcionalmente, escopos adicionais são adicionados com `options.Scope.Add("{SCOPE}");` , em que o espaço reservado `{SCOPE}` é o escopo adicional a ser adicionado.

Opcionalmente, o recurso é especificado com `options.Resource = "{RESOURCE}";` , onde o espaço reservado `{RESOURCE}` é o recurso. Por exemplo:

```csharp
options.Resource = "https://graph.microsoft.com";
```

Defina uma classe para passar o estado inicial do aplicativo com os tokens de acesso e de atualização:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Defina um serviço de provedor de token com **escopo** que possa ser usado no Blazor aplicativo para resolver os tokens da [injeção de dependência (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

No `Startup.ConfigureServices` , adicione serviços para:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

No `_Host.cshtml` arquivo, crie e instância do `InitialApplicationState` e passe-o como um parâmetro para o aplicativo:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

No `App` componente ( `App.razor` ), resolva o serviço e inicialize-o com os dados do parâmetro:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Adicione uma referência de pacote ao aplicativo para o [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pacote NuGet.

No serviço que faz uma solicitação de API segura, insira o provedor de token e recupere o token para a solicitação de API:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Definir o esquema de autenticação</h2>

Para um aplicativo que usa mais de um middleware de autenticação e, portanto, tem mais de um esquema de autenticação, o esquema que o Blazor usa pode ser definido explicitamente na configuração do ponto de extremidade de `Startup.Configure` . O exemplo a seguir define o esquema de Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Usar pontos de extremidade do OpenID Connect (OIDC) v 2.0

Nas versões do ASP.NET Core anteriores à 5,0, a biblioteca de autenticação e os Blazor modelos usam pontos de extremidade do OpenID Connect (OIDC) v 1.0. Para usar um ponto de extremidade v 2.0 com versões do ASP.NET Core anteriores a 5,0, configure a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opção no <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo ( `appsettings.json` ):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriedade diretamente. Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ou no arquivo de configurações do aplicativo com a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> chave.

### <a name="code-changes"></a>Alterações de código

* A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0. Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) na documentação do Azure.
* Como os recursos são especificados em URIs de escopo para pontos de extremidade v 2.0, remova a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> configuração de propriedade em <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  Para obter mais informações, consulte [escopos, não recursos](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) na documentação do Azure.

### <a name="app-id-uri"></a>URI da ID do aplicativo

* Ao usar pontos de extremidade v 2.0, as APIs definem um *`App ID URI`* , que deve representar um identificador exclusivo para a API.
* Todos os escopos incluem o URI da ID do aplicativo como um prefixo, e os pontos de extremidade v 2.0 emitem tokens de acesso com o URI da ID do aplicativo como o público-alvo.
* Ao usar pontos de extremidade V 2.0, a ID do cliente configurada na API do servidor muda da ID do aplicativo de API (ID do cliente) para o URI da ID do aplicativo.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

Você pode encontrar o URI da ID do aplicativo a ser usado na descrição do registro do aplicativo do provedor OIDC.

::: moniker-end
