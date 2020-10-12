---
title: ASP.NET Core Blazor WebAssembly com grupos e funções de Azure Active Directory
author: guardrex
description: Saiba como configurar o Blazor WebAssembly para usar grupos de Azure Active Directory e funções.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ac666a4c7493140d4ae93047e18202c3d8314c7b
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900694"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Azure Active Directory (AAD) grupos, funções de administrador e funções definidas pelo usuário

De [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) fornece várias abordagens de autorização que podem ser combinadas com ASP.NET Core Identity :

* Grupos definidos pelo usuário
  * Segurança
  * Microsoft 365
  * Distribuição
* Funções
  * Funções de administrador do AAD
  * Funções definidas pelo usuário

As diretrizes neste artigo se aplicam aos Blazor WebAssembly cenários de implantação do AAD descritos nos tópicos a seguir:

* [Aplicativo autônomo com contas Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Aplicativo autônomo com o AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Aplicativo hospedado com o AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Permissão de API Microsoft Graph

Uma chamada à [API Microsoft Graph](/graph/use-the-api) é necessária para qualquer usuário de aplicativo com mais de cinco associações de grupo de segurança e função de administrador do AAD.

Para permitir chamadas de API do Graph, conceda ao *`Client`* aplicativo autônomo ou de uma solução hospedada Blazor qualquer um dos seguintes [API do Graph permissões](/graph/permissions-reference) no portal do Azure:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` é a permissão com privilégios mínimos e é a permissão usada para o exemplo descrito neste artigo.

## <a name="user-defined-groups-and-administrator-roles"></a>Grupos definidos pelo usuário e funções de administrador

Para configurar o aplicativo no portal do Azure para fornecer uma `groups` declaração de associação, consulte os seguintes artigos do Azure. Atribuir usuários a grupos do AAD definidos pelo usuário e funções de administrador do AAD.

* [As funções que usam grupos de segurança do Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` Attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Os exemplos a seguir pressupõem que um usuário é atribuído à função de *administrador de cobrança* do AAD.

A única `groups` declaração enviada pelo AAD apresenta os grupos e as funções do usuário como IDs de objeto (GUIDs) em uma matriz JSON. O aplicativo deve converter a matriz JSON de grupos e funções em declarações individuais nas `group` quais o aplicativo pode criar [políticas](xref:security/authorization/policies) .

Quando o número de funções de administrador do AAD atribuído e grupos definidos pelo usuário exceder cinco, o AAD enviará uma `hasgroups` declaração com um `true` valor em vez de enviar uma `groups` declaração. Qualquer aplicativo que possa ter mais de cinco funções e grupos atribuídos a seus usuários deve fazer uma chamada API do Graph separada para obter funções e grupos de um usuário. A implementação de exemplo fornecida neste artigo aborda esse cenário. Para obter mais informações, consulte o `groups` e `hasgroups` informações de declarações no artigo [tokens de acesso da plataforma de identidade da Microsoft: declarações de carga](/azure/active-directory/develop/access-tokens#payload-claims) .

Estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir propriedades de matriz para grupos e funções. Atribua uma matriz vazia a cada propriedade para que a verificação de `null` não seja necessária quando essas propriedades forem usadas em `foreach` loops posteriormente.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

No aplicativo autônomo ou no *`Client`* aplicativo de uma solução hospedada Blazor , crie uma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizada. Use o escopo correto (permissão) para chamadas API do Graph que obtenham informações de função e grupo.

`GraphAPIAuthorizationMessageHandler.cs`:

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
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

Em `Program.Main` ( `Program.cs` ), adicione o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> serviço de implementação e adicione um nome <xref:System.Net.Http.HttpClient> para fazer API do Graph solicitações. O exemplo a seguir nomeia o cliente `GraphAPI` :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Crie classes de objetos de diretório do AAD para receber as funções e grupos de Protocolo Open Data (OData) de uma chamada de API do Graph. O OData chega no formato JSON e uma chamada para <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popula uma instância da `DirectoryObjects` classe.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

Crie uma fábrica de usuário personalizada para processar declarações de funções e grupos. A implementação de exemplo a seguir também manipula a `roles` matriz de declaração, que é abordada na seção [funções definidas pelo usuário](#user-defined-roles) . Se a `hasgroups` declaração estiver presente, o nome <xref:System.Net.Http.HttpClient> será usado para fazer uma solicitação autorizada para API do Graph obter as funções e os grupos do usuário. Essa implementação usa o ponto de extremidade do Microsoft Identity Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentação da API](/graph/api/user-list-memberof)). As diretrizes neste tópico serão atualizadas para Identity o v 2.0 quando os pacotes MSAL forem atualizados para o v 2.0.

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Não há necessidade de fornecer código para remover a declaração original `groups` , se presente, porque ela é automaticamente removida pela estrutura.

> [!NOTE]
> A abordagem neste exemplo:
>
> * Adiciona uma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizada para anexar tokens de acesso a solicitações de saída.
> * Adiciona um nome <xref:System.Net.Http.HttpClient> para fazer solicitações da API Web a um ponto de extremidade de API Web externo seguro.
> * Usa o nome <xref:System.Net.Http.HttpClient> para fazer solicitações autorizadas.
>
> A cobertura geral para essa abordagem é encontrada no <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artigo.

Registre a fábrica no `Program.Main` ( `Program.cs` ) do aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor . Consentimento para o `Directory.Read.All` escopo de permissão como um escopo adicional para o aplicativo:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Crie uma [política](xref:security/authorization/policies) para cada grupo ou função no `Program.Main` . O exemplo a seguir cria uma política para a função de *administrador de cobrança* do AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Para obter a lista completa de IDs de objeto de função do AAD, consulte a seção [IDs de objeto da função de administrador do AAD](#aad-administrator-role-object-ids) .

Nos exemplos a seguir, o aplicativo usa a política anterior para autorizar o usuário.

O [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funciona com a política:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

O acesso a um componente inteiro pode ser baseado na política usando a [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Se o usuário não estiver conectado, ele será redirecionado para a página de entrada do AAD e, em seguida, de volta para o componente depois de entrar.

Uma verificação de política também pode ser [executada no código com lógica de procedimento](xref:blazor/security/index#procedural-logic):

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Autorizar o acesso à API do servidor para grupos definidos pelo usuário e funções de administrador

Além de autorizar usuários no aplicativo Webassembly do lado do cliente para acessar páginas e recursos, a API do servidor pode autorizar os usuários a acessar pontos de extremidade de API seguros. Depois que o aplicativo de *servidor* valida o token de acesso do usuário:

* O aplicativo usa a declaração de identificador de [objeto imutável ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) do usuário do JWT ( `id_token` ) para obter um token de acesso para API do Graph.
* Uma chamada de API do Graph Obtém o grupo de segurança definido pelo usuário do Azure e as associações de função de administrador do usuário.
* As associações são usadas para estabelecer `group` declarações.
* [As políticas de autorização](xref:security/authorization/policies) podem ser usadas para limitar o acesso do usuário aos pontos de extremidade da API do servidor.

> [!NOTE]
> Atualmente, essas diretrizes não incluem a autorização de usuários com base em suas [funções do AAD definidas pelo usuário](#user-defined-roles).

### <a name="packages"></a>Pacotes

Adicione referências de pacote ao aplicativo de *servidor* para os seguintes pacotes:

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="azure-configuration"></a>Configuração do Azure

* Confirme se o registro do aplicativo do *servidor* recebe acesso à API para a permissão API do Graph para `Directory.Read.All` , que é o nível de acesso com privilégios mínimos para grupos de segurança. Confirme se o consentimento do administrador é aplicado à permissão depois de fazer a atribuição de permissão.
* Atribua um novo segredo do cliente ao aplicativo do *servidor* . Observe o segredo para a configuração do aplicativo na seção [configurações do aplicativo](#app-settings) .

### <a name="app-settings"></a>Configurações do aplicativo

No arquivo de configurações do aplicativo ( `appsettings.json` ou `appsettings.Production.json` ), crie uma `ClientSecret` entrada com o segredo do cliente do aplicativo do *servidor* do portal do Azure:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Por exemplo:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a>Políticas de autorização

Crie [políticas de autorização](xref:security/authorization/policies) para grupos de segurança do AAD e funções de administrador do AAD no aplicativo do *servidor* `Startup.ConfigureServices` ( `Startup.cs` ) com base em IDs de objeto de grupo e [IDs de objeto de função de administrador do AAD](#aad-administrator-role-object-ids).

Por exemplo, uma política de função de administrador de cobrança do Azure tem a seguinte configuração:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Para obter mais informações, consulte <xref:security/authorization/policies>.

### <a name="controller-access"></a>Acesso ao controlador

Exigir políticas nos controladores do aplicativo do *servidor* .

O exemplo a seguir limita o acesso a dados de cobrança do `BillingDataController` para administradores de cobrança do Azure com um nome de política de `BillingAdmin` , conforme configurado na seção [políticas de autorização](#authorization-policies) :

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a>Configuração de serviço

No método do aplicativo do *servidor* `Startup.ConfigureServices` , adicione lógica para fazer com que o API do Graph chame e estabeleça `group` declarações de usuário para grupos de segurança e funções do usuário.

> [!NOTE]
> O código de exemplo nesta seção usa o Biblioteca de Autenticação do Active Directory (ADAL), que é baseado na Identity plataforma Microsoft v 1.0. Este tópico será atualizado para o Identity v 2.0 para .NET 5. Acompanhe o progresso desse trabalho monitorando [[RC1] Microsoft Identity Platform 2,0 para Blazor (dotNet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).

Namespaces adicionais são necessários para o código na `Startup` classe do aplicativo do *servidor* . O conjunto de instruções a seguir `using` inclui os namespaces necessários para o código a seguir nesta seção:

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

Ao configurar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Opcionalmente, inclua o processamento para <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Por exemplo, o aplicativo pode registrar em log a autenticação com falha.
* No <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , faça uma chamada de API do Graph para obter os grupos e as funções do usuário.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornece informações de identificação pessoal (PII) em mensagens de log. Ative somente PII para depuração com contas de usuário de teste.

Em `Startup.ConfigureServices`:

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

No exemplo anterior:

* A tentativa de aquisição de token silenciosa ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ) é tentada primeiro porque o token de acesso pode já ter sido armazenado no cache de token do Adal. É mais rápido obter o token do cache do que solicitar um novo token.
* Se o token de acesso não for adquirido do cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> ou <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> for gerado), uma declaração de usuário ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) será feita com a credencial do cliente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) para obter o token em nome do usuário ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ). Em seguida, o `Microsoft.Graph.GraphServiceClient` pode continuar a usar o token para fazer a chamada de API do Graph. O token é colocado no cache de token do ADAL. Para chamadas de API do Graph futuras para o mesmo usuário, o token é adquirido do cache silenciosamente com <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .

O código em <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> não obtém associações transitivas. Para alterar o código para obter associações diretas e transitivas:

* Para a linha de código:

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Substituir a linha anterior por:

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Para a linha de código:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Substituir a linha anterior por:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

O código em <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> não distingue entre os grupos de segurança do AAD e as funções de administrador do AAD ao criar declarações. Para o aplicativo distinguir entre grupos e funções, marque a `entry.ODataType` iteração por meio dos grupos e das funções. Para criar declarações de função e grupo de segurança separados, use um código semelhante ao seguinte:

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a>Funções definidas pelo usuário

Um aplicativo registrado no AAD também pode ser configurado para usar funções definidas pelo usuário.

Para configurar o aplicativo no portal do Azure para fornecer uma `roles` declaração de associação, consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) na documentação do Azure.

O exemplo a seguir pressupõe que um aplicativo está configurado com duas funções:

* `admin`
* `developer`

> [!NOTE]
> Embora não seja possível atribuir funções a grupos de segurança sem uma conta de Azure AD Premium, você pode atribuir usuários a funções e receber uma `roles` declaração para usuários com uma conta padrão do Azure. As diretrizes nesta seção não exigem uma conta de Azure AD Premium.
>
> Várias funções são atribuídas no portal do Azure ao **_adicionar novamente um usuário_** para cada atribuição de função adicional.

A única `roles` declaração enviada pelo AAD apresenta as funções definidas pelo usuário como `appRoles` `value` s em uma matriz JSON. O aplicativo deve converter a matriz JSON de funções em `role` declarações individuais.

O `CustomUserFactory` mostrado na seção [grupos definidos pelo usuário e funções de administrador do AAD](#user-defined-groups-and-administrator-roles) é configurado para agir em uma `roles` declaração com um valor de matriz JSON. Adicione e registre o `CustomUserFactory` no aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor , conforme mostrado na seção [grupos definidos pelo usuário e funções de administrador do AAD](#user-defined-groups-and-administrator-roles) . Não é necessário fornecer código para remover a `roles` declaração original porque ela é automaticamente removida pela estrutura.

No `Program.Main` aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor , especifique a declaração denominada " `role` " como a declaração de função:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

As abordagens de autorização de componente são funcionais neste ponto. Qualquer um dos mecanismos de autorização nos componentes pode usar a `admin` função para autorizar o usuário:

* [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )
* [Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )

  Há suporte para vários testes de função:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a>IDs de objeto de função de administrador do AAD

As IDs de objeto apresentadas na tabela a seguir são usadas para criar [políticas](xref:security/authorization/policies) para `group` declarações. As políticas permitem que um aplicativo autorize usuários para várias atividades em um aplicativo. Para obter mais informações, consulte a seção [grupos definidos pelo usuário e funções de administrador do AAD](#user-defined-groups-and-administrator-roles) .

Função de administrador do AAD | ID de objeto
--- | ---
Administrador de aplicativos | fa11557b-4f15-4ddd-85d5-313c7cd74047
Desenvolvedor de aplicativos | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrador de autenticação | 02d110a1-96b1-419e-af87-746461b60ed7
Administrador do Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Administrador da Proteção de Informações do Azure | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrador do conjunto de chaves B2C IEF | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrador da política IEF B2C | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrador de fluxo de usuário B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Administrador de atributos de fluxo de usuário B2C | dd0baca0-a535-48c1-b871-8431abe16452
Administrador de cobrança | 69ff516a-b57d-4697-a429-9de4af7b5609
Administrador de aplicativos de nuvem | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrador de dispositivo em nuvem | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrador de conformidade | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrador de dados de conformidade | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrador de acesso condicional | 8f71a611-137d-49af-87ad-e97f1fd5da76
Aprovador de acesso do Sistema de Proteção de Dados do Cliente | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Administrador do desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Leitores de diretórios | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrador do Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administradores do Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAdministrador de provedor externo | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrador global | a45ba61b-44db-462c-924b-3b2719152588
Leitor global | f6903b21-6aba-4124-b44c-76671796b9d5
Administrador de grupos | 158b3e5a-d89d-460b-92b5-3b34985f0197
Emissor do convite ao convidado | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrador de assistência técnica | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrador do Intune | 79950741-23fa-4189-b2cb-46640601c497
Administrador do Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrador de licenças | 3355458a-e423-44bf-8b98-4ac5e572cea5
Leitor de privacidade do centro de mensagens | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Leitor do Centro de Mensagens | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrador de aplicativos do Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrador de senha | 466e48b7-5d66-4ae5-8911-1a118de74941
Administrador do Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Administrador do Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrador de autenticação privilegiada | 0829f731-b46d-419F-9742-aeb122367d11
Administrador de função com privilégios | f20a725a-d1c8-4107-83ea-1171c97d00c7
Leitor de relatórios | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrador de pesquisas | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor de pesquisa | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrador de segurança | 20fa50e3-6531-44d8-bd39-b251420568ad
Operador de segurança | 43aae017-8e51-4188-91ab-e6debd572800
Leitor de segurança | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrador de suporte a serviço | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrador do SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrador do Skype for Business | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administrador de Comunicações do Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Engenheiro de Suporte de Comunicações do Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Especialista em comunicações de equipes | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrador de Serviços do Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrador de usuários | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
