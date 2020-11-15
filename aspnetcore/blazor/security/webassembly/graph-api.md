---
title: Usar API do Graph com ASP.NET Core Blazor WebAssembly
author: guardrex
description: Saiba como usar API do Graph com Blazor aplicativos WebAssemlby.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 6464b80d52837e7fe35efe5daac2193b77e21c84
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637646"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>Usar API do Graph com ASP.NET Core Blazor WebAssembly

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

[Microsoft Graph API](/graph/use-the-api) é uma API Web RESTful que habilita Blazor e outros aplicativos .NET Framework para acessar os recursos do serviço Microsoft Cloud.

## <a name="graph-sdk"></a>SDK do Graph

Os [SDKs do Microsoft Graph](/graph/sdks/sdks-overview) foram projetados para simplificar a criação de aplicativos de alta qualidade, eficientes e resilientes que acessam Microsoft Graph.

Os exemplos nesta seção exigem referências de pacote para os seguintes pacotes no arquivo de projeto do arquivo de projeto autônomo ou *`Client`* do aplicativo:

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

As seguintes classes de utilitário e configuração são usadas em cada uma das seguintes subseções deste artigo:

* [Chamar API do Graph de um componente usando o SDK do Graph](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Personalizar declarações de usuário com o SDK do Graph](#customize-user-claims-with-the-graph-sdk)

Depois de adicionar os escopos de API Microsoft Graph na área do AAD do portal do Azure:

* Adicione a seguinte `GraphClientExtensions.cs` classe ao aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor .
* Forneça os escopos necessários para a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> Propriedade do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> no `AuthenticateRequestAsync` método. No exemplo a seguir, o `User.Read` escopo é especificado para corresponder aos exemplos nas seções posteriores deste artigo.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

O espaço reservado `{STRING ARRAY OF SCOPES}` no código anterior é uma matriz de cadeia de caracteres dos escopos permitidos. Por exemplo, defina `Scopes` como o `User.Read` escopo dos exemplos nas seguintes seções deste artigo:

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

Em `Program.Main` ( `Program.cs` ), adicione os serviços de cliente do Graph e a configuração com o `AddGraphClient` método de extensão:

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

O espaço reservado `{STRING ARRAY OF SCOPES}` no código anterior é uma matriz de cadeia de caracteres dos escopos permitidos. Por exemplo, passe o `User.Read` escopo para `AddGraphClient` para os exemplos nas seguintes seções deste artigo:

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Chamar API do Graph de um componente usando o SDK do Graph

Esta seção usa as [classes utilitárias ( `GraphClientExtensions.cs` )](#graph-sdk) descritas anteriormente neste artigo. O componente a seguir `GraphExample` usa um injetado `GraphServiceClient` para obter os dados do perfil AAD do usuário e exibir seu número de telefone celular:

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a>Personalizar declarações de usuário com o SDK do Graph

Esta seção usa as [classes utilitárias ( `GraphClientExtensions.cs` )](#graph-sdk) descritas anteriormente neste artigo.

No exemplo a seguir, o aplicativo cria uma declaração de número de telefone celular para um usuário do número de telefone celular do perfil de usuário do AAD. O aplicativo deve ter o `User.Read` escopo de API do Graph configurado no AAD.

No seguinte alocador de conta de usuário personalizada, a estrutura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> representa a conta do usuário. Se o aplicativo exigir uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir.

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Em `Program.Main` ( `Program.cs` ), configure a autenticação MSAL para usar o alocador de conta de usuário personalizada: se o aplicativo usar uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a>Cliente nomeado com API do Graph

Os exemplos nesta seção usam um nome <xref:System.Net.Http.HttpClient> para API do Graph para obter o número de telefone celular de um usuário para processar uma chamada.

Os exemplos nesta seção exigem uma referência de pacote para [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) no arquivo de projeto do arquivo de projeto do aplicativo ou autônomo *`Client`* .

Crie a seguinte configuração de classe e projeto para trabalhar com API do Graph. A seguinte classe e configuração são usadas em cada uma das seguintes subseções deste artigo:

* [Chamar API do Graph de um componente](#call-graph-api-from-a-component)
* [Personalizar declarações de usuário com API do Graph e um cliente nomeado](#customize-user-claims-with-graph-api-and-a-named-client)

Depois de adicionar os escopos de API Microsoft Graph na área do AAD do portal do Azure, forneça os escopos necessários ao manipulador configurado do aplicativo para API do Graph. O exemplo a seguir configura o manipulador para o `User.Read` escopo. Escopos adicionais podem ser adicionados.

`GraphAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

Em `Program.Main` ( `Program.cs` ), configure o nome <xref:System.Net.Http.HttpClient> para API do Graph:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Chamar API do Graph de um componente

Esta seção usa o [manipulador de mensagens de autorização do grafo ( `GraphAuthorizationMessageHandler.cs` ) e as `Program.Main` adições ao aplicativo](#named-client-with-graph-api) descrito anteriormente neste artigo, que fornece um nome <xref:System.Net.Http.HttpClient> para API do Graph.

Em um Razor componente:

* Crie um <xref:System.Net.Http.HttpClient> para API do Graph e emita uma solicitação para os dados de perfil do usuário.
* A `UserInfo.cs` classe designa as propriedades de perfil de usuário necessárias com o <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> atributo e o nome JSON usado pelo AAD para essas propriedades.

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> No exemplo anterior, o desenvolvedor implementa o personalizado `ICallProcessor` ( `CallProcessor` ) para a fila e, em seguida, coloca as chamadas automatizadas.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Personalizar declarações de usuário com API do Graph e um cliente nomeado

Esta seção usa o [manipulador de mensagens de autorização do grafo ( `GraphAuthorizationMessageHandler.cs` ) e as `Program.Main` adições ao aplicativo](#named-client-with-graph-api) descrito anteriormente neste artigo, que fornece um nome <xref:System.Net.Http.HttpClient> para API do Graph.

No exemplo a seguir, o aplicativo cria uma declaração de número de telefone celular para o usuário do número de telefone celular do perfil de usuário do AAD. O aplicativo deve ter o `User.Read` escopo de API do Graph configurado no AAD.

Adicione uma `UserInfo.cs` classe ao aplicativo e designe as propriedades de perfil de usuário necessárias com o <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> atributo e o nome JSON usado pelo AAD para essas propriedades:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

No seguinte alocador de conta de usuário personalizada, a estrutura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> representa a conta do usuário. Se o aplicativo exigir uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir.

`CustomAccountFactory.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Em `Program.Main` ( `Program.cs` ), configure a autenticação MSAL para usar o alocador de conta de usuário personalizada: se o aplicativo usar uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

O exemplo anterior é para um aplicativo que usa a autenticação do AAD com MSAL. Existem padrões semelhantes para OIDC e autenticação de API. Para obter mais informações, consulte a seção exemplos em [Personalizar o usuário com uma declaração de carga](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) .
