---
title: ASP.NET Core Blazor WebAssembly cenários de segurança adicionais
author: guardrex
description: Saiba como configurar o Blazor WebAssembly para cenários de segurança adicionais.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 2881b5d01f3b2e41659e3166a4e77b64a450f017
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592911"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly cenários de segurança adicionais

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Anexar tokens a solicitações de saída

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> é <xref:System.Net.Http.DelegatingHandler> usado para anexar tokens de acesso a instâncias de saída <xref:System.Net.Http.HttpResponseMessage> . Os tokens são adquiridos usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> serviço, que é registrado pela estrutura. Se um token não puder ser adquirido, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> será gerado. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> tem um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.

Para sua conveniência, a estrutura fornece o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> pré-configurado com o endereço base do aplicativo como uma URL autorizada. **Tokens de acesso são adicionados somente quando o URI de solicitação está dentro do URI base do aplicativo.** Quando os URIs de solicitação de saída não estiverem dentro do URI base do aplicativo, use uma [ `AuthorizationMessageHandler` classe personalizada (*recomendado*)](#custom-authorizationmessagehandler-class) ou [Configure o `AuthorizationMessageHandler` ](#configure-authorizationmessagehandler).

> [!NOTE]
> Além da configuração do aplicativo cliente para acesso à API do servidor, a API do servidor também deve permitir solicitações entre origens (CORS) quando o cliente e o servidor não residem no mesmo endereço base. Para obter mais informações sobre a configuração CORS do lado do servidor, consulte a seção [CORS (compartilhamento de recursos entre origens)](#cross-origin-resource-sharing-cors) mais adiante neste artigo.

No exemplo a seguir:

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> Adiciona <xref:System.Net.Http.IHttpClientFactory> e serviços relacionados à coleção de serviços e configura um nome <xref:System.Net.Http.HttpClient> ( `ServerAPI` ). <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> é o endereço base do URI do recurso ao enviar solicitações. <xref:System.Net.Http.IHttpClientFactory> é fornecido pelo [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote NuGet.
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> é <xref:System.Net.Http.DelegatingHandler> usado para anexar tokens de acesso a instâncias de saída <xref:System.Net.Http.HttpResponseMessage> . Tokens de acesso são adicionados somente quando o URI de solicitação está dentro do URI base do aplicativo.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> Cria e configura uma <xref:System.Net.Http.HttpClient> instância para solicitações de saída usando a configuração que corresponde ao nome <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, os URIs de solicitação estão dentro do URI base do aplicativo por padrão. Portanto, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> em um aplicativo gerado a partir do modelo de projeto.

O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) padrão:

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a>`AuthorizationMessageHandler`Classe personalizada

*Estas diretrizes nesta seção são recomendadas para aplicativos cliente que fazem solicitações de saída para URIs que não estão dentro do URI base do aplicativo.*

No exemplo a seguir, uma classe personalizada se estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> para uso como <xref:System.Net.Http.DelegatingHandler> para um <xref:System.Net.Http.HttpClient> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura esse manipulador para autorizar solicitações HTTP de saída usando um token de acesso. O token de acesso só será anexado se pelo menos uma das URLs autorizadas for uma base do URI de solicitação ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

No `Program.Main` ( `Program.cs` ), o `CustomAuthorizationMessageHandler` é registrado como um serviço com escopo e é configurado como o <xref:System.Net.Http.DelegatingHandler> para instâncias de saída <xref:System.Net.Http.HttpResponseMessage> feitas por um nome <xref:System.Net.Http.HttpClient> :

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.

O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) padrão. Em que o cliente é criado com <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote), as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações para a API do servidor. Se o URI de solicitação for um URI relativo, como no exemplo a seguir ( `ExampleAPIMethod` ), ele será combinado com o <xref:System.Net.Http.HttpClient.BaseAddress> quando o aplicativo cliente fizer a solicitação:

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a>configurar `AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pode ser configurado com URLs autorizadas, escopos e uma URL de retorno usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> método. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura o manipulador para autorizar solicitações HTTP de saída usando um token de acesso. O token de acesso só será anexado se pelo menos uma das URLs autorizadas for uma base do URI de solicitação ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ). Se o URI de solicitação for um URI relativo, ele será combinado com o <xref:System.Net.Http.HttpClient.BaseAddress> .

No exemplo a seguir, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura um <xref:System.Net.Http.HttpClient> in `Program.Main` ( `Program.cs` ):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> é atribuído ao seguinte por padrão:

* O <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Uma URL da `authorizedUrls` matriz.

### <a name="graph-api-example"></a>Exemplo de API do Graph

No exemplo a seguir, um nome <xref:System.Net.Http.HttpClient> para API do Graph é usado para obter o número de telefone celular de um usuário para processar uma chamada. Depois de adicionar a permissão Microsoft Graph API `User.Read` na área do AAD do portal do Azure, o escopo é configurado para o cliente nomeado no aplicativo autônomo ou aplicativo cliente de uma solução hospedada Blazor .

> [!NOTE]
> O exemplo nesta seção Obtém API do Graph dados para o usuário no código do *componente*. Para criar declarações de usuário do API do Graph, consulte os seguintes recursos:
>
> * [Personalizar a seção de usuário](#customize-the-user)
> * <xref:blazor/security/webassembly/aad-groups-roles>

`GraphAuthorizationMessageHandler.cs`:

```csharp
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

Em `Program.Main` ( `Program.cs` ):

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Em um Razor componente ( `Pages/CallUser.razor` ):

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

## <a name="typed-httpclient"></a>Automaticamente `HttpClient`

Um cliente tipado pode ser definido para lidar com todas as preocupações de aquisição de token e HTTP em uma única classe.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `using static BlazorSample.Data;` ).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.

`FetchData` componente ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Configurar o `HttpClient` manipulador

O manipulador pode ser configurado ainda mais com <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para solicitações HTTP de saída.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> é atribuído ao seguinte por padrão:

* O <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Uma URL da `authorizedUrls` matriz.

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro

Se o Blazor WebAssembly aplicativo normalmente usa um padrão seguro <xref:System.Net.Http.HttpClient> , o aplicativo também pode fazer solicitações de API da Web não autenticadas ou não autorizadas Configurando um nome <xref:System.Net.Http.HttpClient> :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.

O registro anterior é além do registro padrão seguro existente <xref:System.Net.Http.HttpClient> .

Um componente cria o <xref:System.Net.Http.HttpClient> do <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote) para fazer solicitações não autenticadas ou não autorizadas:

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> O controlador na API do servidor, `WeatherForecastNoAuthenticationController` para o exemplo anterior, não está marcado com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.

A decisão de usar um cliente seguro ou um cliente inseguro, pois a <xref:System.Net.Http.HttpClient> instância padrão é até o desenvolvedor. Uma maneira de tomar essa decisão é considerar o número de pontos de extremidade autenticados versus não autenticados que o aplicativo contata. Se a maioria das solicitações do aplicativo for proteger pontos de extremidade de API, use a instância autenticada <xref:System.Net.Http.HttpClient> como o padrão. Caso contrário, registre a instância não autenticada <xref:System.Net.Http.HttpClient> como o padrão.

Uma abordagem alternativa para usar o <xref:System.Net.Http.IHttpClientFactory> é criar um [cliente tipado](#typed-httpclient) para acesso não autenticado a pontos de extremidade anônimos.

## <a name="request-additional-access-tokens"></a>Solicitar tokens de acesso adicionais

Os tokens de acesso podem ser obtidos manualmente chamando `IAccessTokenProvider.RequestAccessToken` . No exemplo a seguir, um escopo adicional é exigido por um aplicativo para o padrão <xref:System.Net.Http.HttpClient> . O exemplo da MSAL (biblioteca de autenticação da Microsoft) configura o escopo com `MsalProviderOptions` :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

Os `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaços reservados e no exemplo anterior são Escopos personalizados.

O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos.

Em um Razor componente:

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

Os `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaços reservados e no exemplo anterior são Escopos personalizados.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> apresenta

* `true` com o `token` para uso.
* `false` Se o token não for recuperado.

## <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

Ao enviar credenciais ( cookie s/cabeçalhos de autorização) em solicitações CORS, o `Authorization` cabeçalho deve ser permitido pela política CORS.

A política a seguir inclui a configuração para o:

* Origens da solicitação ( `http://localhost:5000` , `https://localhost:5001` ).
* Qualquer método (verbo).
* `Content-Type``Authorization`cabeçalhos e. Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header` ), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Credenciais definidas pelo código JavaScript do lado do cliente ( `credentials` propriedade definida como `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Uma solução hospedada Blazor com base no Blazor modelo de projeto hospedado usa o mesmo endereço base para os aplicativos cliente e servidor. O aplicativo cliente <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> é definido como um URI de `builder.HostEnvironment.BaseAddress` por padrão. A configuração de CORS **não** é necessária na configuração padrão de um aplicativo hospedado criado a partir do Blazor modelo de projeto hospedado. Aplicativos cliente adicionais que não são hospedados pelo projeto do servidor e que não compartilham **o endereço base do aplicativo do servidor** exigem a configuração CORS no projeto do servidor.

Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo ( `Components/HTTPRequestTester.razor` ).

## <a name="handle-token-request-errors"></a>Manipular erros de solicitação de token

Quando um aplicativo de página única (SPA) autentica um usuário usando o OpenID Connect (OIDC), o estado de autenticação é mantido localmente no SPA e no Identity provedor (IP) na forma de uma sessão cookie definida como resultado do usuário que fornece suas credenciais.

Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens. Caso contrário, o usuário será desconectado após os tokens concedidos expirarem. Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.

Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP. Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido. Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.

Esses cenários não são específicos para a autenticação baseada em token. Eles fazem parte da natureza do SPAs. Um SPA usando cookie s também falha ao chamar uma API de servidor se a autenticação cookie for removida.

Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:

* Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.
* Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.

Quando o aplicativo solicita um token, há dois resultados possíveis:

* A solicitação é realizada com sucesso e o aplicativo tem um token válido.
* A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.

Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento. Existem várias abordagens com níveis crescentes de complexidade:

* Armazene o estado da página atual no armazenamento de sessão. Durante o [ `OnInitializedAsync` evento de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verifique se o estado pode ser restaurado antes de continuar.
* Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.
* Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.

O exemplo a seguir mostra como:

* Preserve o estado antes de redirecionar para a página de logon.
* Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>Salvar o estado do aplicativo antes de uma operação de autenticação

Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP. Esse pode ser o caso quando você estiver usando um contêiner de estado e quiser restaurar o estado depois que a autenticação for realizada com sucesso. Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito. O exemplo a seguir demonstra a abordagem.

Uma classe de contêiner de estado é criada no aplicativo com propriedades para manter os valores de estado do aplicativo. No exemplo a seguir, o contêiner é usado para manter o valor do contador do componente padrão do modelo de projeto `Counter` ( `Pages/Counter.razor` ). Os métodos para serialização e desserialização do contêiner se baseiam em <xref:System.Text.Json> .

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

O `Counter` componente usa o contêiner de estado para manter o `currentCount` valor fora do componente:

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

Crie um `ApplicationAuthenticationState` de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> . Forneça uma `Id` propriedade, que serve como um identificador para o estado armazenado localmente.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

O `Authentication` componente ( `Pages/Authentication.razor` ) salva e restaura o estado do aplicativo usando o armazenamento de sessão local com os `StateContainer` métodos de serialização e desserialização, `GetStateForLocalStorage` e `SetStateFromLocalStorage` :

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

Este exemplo usa Azure Active Directory (AAD) para autenticação. Em `Program.Main` ( `Program.cs` ):

* O `ApplicationAuthenticationState` é configurado como o tipo de MSAL (biblioteca do Microsoft autenticação) `RemoteAuthenticationState` .
* O contêiner de estado é registrado no contêiner de serviço.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Personalizar rotas de aplicativo

Por padrão, a [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteca usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.

| Rota                            | Finalidade |
| -------------------------------- | ------- |
| `authentication/login`           | Dispara uma operação de entrada. |
| `authentication/login-callback`  | Manipula o resultado de qualquer operação de entrada. |
| `authentication/login-failed`    | Exibe mensagens de erro quando a operação de entrada falha por algum motivo. |
| `authentication/logout`          | Dispara uma operação de saída. |
| `authentication/logout-callback` | Lida com o resultado de uma operação de saída. |
| `authentication/logout-failed`   | Exibe mensagens de erro quando a operação de saída falha por algum motivo. |
| `authentication/logged-out`      | Indica que o usuário fez logoff com êxito. |
| `authentication/profile`         | Dispara uma operação para editar o perfil do usuário. |
| `authentication/register`        | Dispara uma operação para registrar um novo usuário. |

As rotas mostradas na tabela anterior são configuráveis via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.

No exemplo a seguir, todos os caminhos são prefixados com `/security` .

`Authentication` componente ( `Pages/Authentication.razor` ):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> com um parâmetro de ação explícita:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.

## <a name="customize-the-authentication-user-interface"></a>Personalizar a interface do usuário de autenticação

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação. Cada Estado pode ser personalizado passando um personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> . Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o da <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> seguinte maneira.

`Authentication` componente ( `Pages/Authentication.razor` ):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.

| Rota                            | Fragmento                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a>Personalizar o usuário

Os usuários associados ao aplicativo podem ser personalizados.

### <a name="customize-the-user-with-a-payload-claim"></a>Personalizar o usuário com uma declaração de carga

No exemplo a seguir, os usuários autenticados do aplicativo recebem uma `amr` declaração para cada um dos métodos de autenticação do usuário. A `amr` declaração identifica como o assunto do token foi autenticado nas declarações de Identity [carga](/azure/active-directory/develop/access-tokens#the-amr-claim)do Microsoft Platform v 1.0. O exemplo usa uma classe de conta de usuário personalizada baseada em <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .

Crie uma classe que estenda a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe. O exemplo a seguir define a `AuthenticationMethod` propriedade como a matriz do usuário de `amr` valores de propriedade JSON. `AuthenticationMethod` é preenchido automaticamente pela estrutura quando o usuário é autenticado.

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Crie uma fábrica que se estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> para criar declarações dos métodos de autenticação do usuário armazenados no `CustomUserAccount.AuthenticationMethod` :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

Registre o `CustomAccountFactory` para o provedor de autenticação em uso. Qualquer um dos seguintes registros é válido:

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

### <a name="customize-the-user-with-graph-api-claims"></a>Personalizar o usuário com declarações de API do Graph

No exemplo a seguir, o aplicativo cria uma declaração de número de telefone celular para o usuário de API do Graph usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> . O aplicativo deve ter a `User.Read` permissão API do Graph (escopo) configurada no AAD.

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

Um nome <xref:System.Net.Http.HttpClient> para API do Graph é criado em `Program.Main` ( `Program.cs` ) usando `GraphAPIAuthorizationMessageHandler` :

```csharp
using System;

...

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

`Models/UserInfo.cs`:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

No seguinte `CustomAccountFactory` ( `CustomAccountFactory.cs` ), a estrutura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> representa a conta do usuário. Se o aplicativo exigir uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir:

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
                    userIdentity.AddClaim(new Claim("mobilephone", userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {MESSAGE}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Em `Program.Main` ( `Program.cs` ), configure o aplicativo para usar a fábrica personalizada. Se o aplicativo usar uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir:

```csharp
using Microsoft.Extensions.Configuration;
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

O exemplo anterior é para um aplicativo que usa a autenticação do AAD com MSAL. Existem padrões semelhantes para OIDC e autenticação de API. Para obter mais informações, consulte os exemplos no final da seção [Personalizar o usuário com uma declaração de carga](#customize-the-user-with-a-payload-claim) .

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a>Grupos de segurança do AAD e funções com uma classe de conta de usuário personalizada

Para obter um exemplo adicional que funciona com grupos de segurança do AAD e funções de administrador do AAD e uma classe de conta de usuário personalizada, consulte <xref:blazor/security/webassembly/aad-groups-roles> .

## <a name="support-prerendering-with-authentication"></a>Suporte ao pré-processamento com autenticação

Depois de seguir as diretrizes em um dos Blazor WebAssembly Tópicos do aplicativo hospedado, use as instruções a seguir para criar um aplicativo que:

* Processa caminhos para os quais a autorização não é necessária.
* Não PreRender caminhos para os quais a autorização é necessária.

Na classe do aplicativo cliente `Program` ( `Program.cs` ), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices` ):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

No aplicativo do servidor `Startup.ConfigureServices` , registre os seguintes serviços adicionais:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

No método do aplicativo do servidor `Startup.Configure` , substitua [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) por [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

No aplicativo de servidor, crie uma `Pages` pasta se ela não existir. Crie uma `_Host.cshtml` página dentro da pasta do aplicativo do servidor `Pages` . Cole o conteúdo do arquivo do aplicativo cliente `wwwroot/index.html` no `Pages/_Host.cshtml` arquivo. Atualize o conteúdo do arquivo:

* Adicione `@page "_Host"` ao topo do arquivo.
* Substitua a `<app>Loading...</app>` marca pela seguinte:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opções para aplicativos hospedados e provedores de logon de terceiros

Ao autenticar e autorizar um Blazor WebAssembly aplicativo hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário. O que você escolher dependerá de seu cenário.

Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticar usuários para chamar somente APIs de terceiros protegidas

Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Neste cenário:

* O servidor que hospeda o aplicativo não desempenha uma função.
* As APIs no servidor não podem ser protegidas.
* O aplicativo só pode chamar APIs de terceiros protegidas.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros

Configure Identity com um provedor de logon de terceiros. Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.

Quando um usuário faz logon, o Identity coleta tokens de acesso e de atualização como parte do processo de autenticação. Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Usar um token de acesso do servidor para recuperar o token de acesso de terceiros

Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor. A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente do Identity no cliente.

Não recomendamos essa abordagem. Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público. Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial. Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.

* O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.
* Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros

Faça uma chamada de API do cliente para a API do servidor. No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.

Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:

* O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.
* O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Usar pontos de extremidade do OpenID Connect (OIDC) v 2.0

A biblioteca de autenticação e os Blazor modelos de projeto usam pontos de extremidade do OpenID Connect (OIDC) v 1.0. Para usar um ponto de extremidade v 2.0, configure a opção de portador JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> . No exemplo a seguir, o AAD está configurado para v 2.0 acrescentando um `v2.0` segmento à <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Propriedade:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo ( `appsettings.json` ):

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriedade diretamente. Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou no arquivo de configurações do aplicativo ( `appsettings.json` ) com a `Authority` chave.

A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0. Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).

## <a name="configure-and-use-grpc-in-components"></a>Configurar e usar o gRPC em componentes

Para configurar um Blazor WebAssembly aplicativo para usar a [estrutura ASP.NET Core gRPC](xref:grpc/index):

* Habilite o gRPC-Web no servidor. Para obter mais informações, consulte <xref:grpc/browser>.
* Registre os serviços gRPC para o manipulador de mensagens do aplicativo. O exemplo a seguir configura o manipulador de mensagens de autorização do aplicativo para usar o [ `GreeterClient` serviço do tutorial do gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample` ). Coloque o `.proto` arquivo no `Shared` projeto da solução hospedada Blazor .

Um componente no aplicativo cliente pode fazer chamadas gRPC usando o cliente gRPC ( `Pages/Grpc.razor` ):

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample` ). Para usar a `Status.DebugException` propriedade, use [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) versão 2.30.0 ou posterior.

Para obter mais informações, consulte <xref:grpc/browser>.

## <a name="additional-resources"></a>Recursos adicionais

* [`HttpClient` e `HttpRequestMessage` com opções de solicitação de API de busca](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
