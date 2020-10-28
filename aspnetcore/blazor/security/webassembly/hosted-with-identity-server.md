---
title: Proteger um aplicativo ASP.NET Core hospedado Blazor WebAssembly com o Identity servidor
author: guardrex
description: Saiba como proteger um aplicativo ASP.NET Core hospedado Blazor WebAssembly com o Identity servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: a6fd005e19f532089ac1a1914756fb03eabb24c4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690483"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Este artigo explica como criar um [ Blazor WebAssembly aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa o [ Identity servidor do](https://identityserver.io/) para autenticar usuários e chamadas de API.

> [!NOTE]
> Para configurar um aplicativo autônomo ou hospedado Blazor WebAssembly para usar uma instância de servidor externa existente Identity , siga as orientações em <xref:blazor/security/webassembly/standalone-with-authentication-library> .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:

1. Depois de escolher o modelo de **Blazor WebAssembly aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação** .

1. Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .

1. Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/CLI do .NET Core](#tab/visual-studio-code+netcore-cli)

Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia, especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema do ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Espaço reservado  | Exemplo        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:

1. Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .

1. O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .

1. Marque a caixa de seleção **ASP.NET Core hospedado** .

---

## <a name="server-app-configuration"></a>*`Server`* configuração do aplicativo

As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.

### <a name="startup-class"></a>Classe de inicialização

A `Startup` classe tem as seguintes adições.

* Em `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServidor com um <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do Identity servidor:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo Identity servidor:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Em `Startup.Configure`:

  * O Identity middleware do servidor expõe os pontos de extremidade do OpenID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:

    ```csharp
    app.UseAuthentication();
    ```

  * O middleware de autorização habilita os recursos de autorização:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura o [ Identity servidor](https://identityserver.io/) para cenários de ASP.NET Core. IdentityO servidor é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo. IdentityO servidor expõe complexidade desnecessária para os cenários mais comuns. Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida. Depois que a autenticação precisar ser alterada, todo o poder do Identity servidor estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.

### <a name="addno-locidentityserverjwt"></a>Adicionar Identity ServerJwt

O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão. A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL `/Identity` . O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações. Além disso, esse método:

* Registra um `{APPLICATION NAME}API` recurso de API com Identity o servidor com um escopo padrão de `{APPLICATION NAME}API` .
* Configura o middleware do token de portador JWT para validar tokens emitidos pelo Identity servidor para o aplicativo.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é aplicado à classe. O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext

No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se para incluir o esquema para o Identity servidor. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Para obter controle total do esquema de banco de dados, herde de uma das Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.

### <a name="app-settings"></a>Configurações do aplicativo

No arquivo de configurações do aplicativo ( `appsettings.json` ) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados. No exemplo a seguir, há um único cliente. O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth. O perfil indica o tipo de aplicativo que está sendo configurado. O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>*`Client`* configuração do aplicativo

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

### <a name="httpclient-configuration"></a>`HttpClient` configuração

Em `Program.Main` ( `Program.cs` ), um nome <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) é configurado para fornecer <xref:System.Net.Http.HttpClient> instâncias que incluem tokens de acesso ao fazer solicitações para a API do servidor:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Se você estiver configurando um Blazor WebAssembly aplicativo para usar uma Identity instância de servidor existente que não faz parte de uma solução hospedada Blazor , altere o <xref:System.Net.Http.HttpClient> registro do endereço base de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) para a URL do ponto de extremidade de autorização da API do aplicativo do servidor.

### <a name="api-authorization-support"></a>Suporte à autorização de API

O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote. Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.

```csharp
builder.Services.AddApiAuthorization();
```

Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` . Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo. Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:

* Para usuários autenticados:
  * Exibe o nome de usuário atual.
  * Oferece um link para a página de perfil do usuário no ASP.NET Core Identity .
  * Oferece um botão para fazer logoff do aplicativo.
* Para usuários anônimos:
  * Oferece a opção de se registrar.
  * Oferece a opção de fazer logon.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, seja:

* Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .
* Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nome e declaração de função com autorização de API

### <a name="custom-user-factory"></a>Fábrica de usuário personalizada

No *`Client`* aplicativo, crie uma fábrica de usuário personalizada. Identity O servidor envia várias funções como uma matriz JSON em uma única `role` declaração. Uma única função é enviada como um valor de cadeia de caracteres na declaração. A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

No *`Client`* aplicativo, registre a fábrica em `Program.Main` ( `Program.cs` ):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

No *`Server`* aplicativo, chame <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> no Identity Construtor, que adiciona serviços relacionados a funções:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>Configurar Identity servidor

Use **uma** das seguintes abordagens:

* [Opções de autorização de API](#api-authorization-options)
* [Serviço de perfil](#profile-service)

#### <a name="api-authorization-options"></a>Opções de autorização de API

No *`Server`* aplicativo:

* Configure Identity o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.
* Impedir o mapeamento padrão para funções no manipulador de tokens JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Serviço de perfil

No *`Server`* aplicativo, crie uma `ProfileService` implementação.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

No *`Server`* aplicativo, registre o serviço de perfil em `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Usar mecanismos de autorização

No *`Client`* aplicativo, as abordagens de autorização de componente são funcionais neste ponto. Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:

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

`User.Identity.Name` é populado no *`Client`* aplicativo com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a>Hospedar no serviço de Azure App com um domínio personalizado

As diretrizes a seguir explicam como implantar um Blazor WebAssembly aplicativo hospedado com o Identity servidor do para [Azure app serviço](https://azure.microsoft.com/services/app-service/) com um domínio personalizado.

Para esse cenário de hospedagem, **não use o** mesmo certificado para a [ Identity chave de assinatura de token do servidor](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) e a comunicação https segura do site com navegadores:

* Usar certificados diferentes para esses dois requisitos é uma boa prática de segurança porque isola chaves privadas para cada finalidade.
* Os certificados TLS para comunicação com navegadores são gerenciados de forma independente sem afetar a Identity assinatura de token do servidor.
* Quando [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fornece um certificado a um aplicativo do serviço de aplicativo para associação de domínio personalizada, Identity o servidor não pode obter o mesmo certificado de Azure Key Vault para assinatura de token. Embora Identity seja possível configurar o servidor para usar o mesmo certificado TLS de um caminho físico, colocar os certificados de segurança no controle do código-fonte é uma **prática inadequada e deve ser evitado na maioria dos cenários** .

Nas diretrizes a seguir, um certificado autoassinado é criado em Azure Key Vault exclusivamente para Identity assinatura de token do servidor. A Identity configuração do servidor usa o certificado do Key Vault por meio do repositório de certificados do aplicativo `My`  >  `CurrentUser` . Outros certificados usados para tráfego HTTPS com domínios personalizados são criados e configurados separadamente do Identity certificado de autenticação do servidor.

Para configurar um aplicativo, Azure App serviço e Azure Key Vault para hospedar com um domínio personalizado e HTTPS:

1. Crie um [plano do serviço de aplicativo](/azure/app-service/overview-hosting-plans) com um nível de plano `Basic B1` ou superior. O serviço de aplicativo requer uma `Basic B1` camada de serviço ou mais alta para usar domínios personalizados.
1. Crie um certificado PFX para a comunicação segura do navegador do site (protocolo HTTPS) com um nome comum do FQDN (nome de domínio totalmente qualificado) do site que sua organização controla (por exemplo, `www.contoso.com` ). Crie o certificado com:
   * Usos de chave
     * Validação de assinatura digital ( `digitalSignature` )
     * Codificação de chave ( `keyEncipherment` )
   * Uso avançado/estendido de chave
     * Autenticação de cliente (1.3.6.1.5.5.7.3.2)
     * Autenticação de servidor (1.3.6.1.5.5.7.3.1)

   Para criar o certificado, use uma das abordagens a seguir ou qualquer outra ferramenta ou serviço online adequado:

   * [Cofre da Chave do Azure](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [MakeCert no Windows](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Anote a senha, que será usada posteriormente para importar o certificado para o Azure Key Vault.

   Para obter mais informações sobre certificados Azure Key Vault, consulte [Azure Key Vault: certificados](/azure/key-vault/certificates/).
1. Crie um novo Azure Key Vault ou use um cofre de chaves existente em sua assinatura do Azure.
1. Na área **certificados** do Key Vault, importe o certificado do site PFX. Registre a impressão digital do certificado, que será usada na configuração do aplicativo mais tarde.
1. No Azure Key Vault, gere um novo certificado autoassinado para Identity assinatura de token do servidor. Dê ao certificado um **nome de certificado** e **assunto** . O **assunto** é especificado como `CN={COMMON NAME}` , em que o `{COMMON NAME}` espaço reservado é o nome comum do certificado. O nome comum pode ser qualquer cadeia de caracteres alfanumérica. Por exemplo, `CN=IdentityServerSigning` é uma **entidade** de certificado válida. Use as definições de **configuração de política avançada** padrão. Registre a impressão digital do certificado, que será usada na configuração do aplicativo mais tarde.
1. Navegue até Azure App serviço no portal do Azure e crie um novo serviço de aplicativo com a seguinte configuração:
   * **Publicar** conjunto como `Code` .
   * Conjunto de **pilhas em tempo de execução** para o tempo de execução do aplicativo.
   * Para **SKU e tamanho** , confirme se a camada do serviço de aplicativo é `Basic B1` ou superior.  O serviço de aplicativo requer uma `Basic B1` camada de serviço ou mais alta para usar domínios personalizados.
1. Depois que o Azure criar o serviço de aplicativo, abra a **configuração** do aplicativo e adicione uma nova configuração de aplicativo especificando as impressões digitais do certificado registradas anteriormente. A chave de configuração do aplicativo é `WEBSITE_LOAD_CERTIFICATES` . Separe as impressões digitais do certificado no valor de configuração do aplicativo com uma vírgula, como mostra o exemplo a seguir:
   * Chave: `WEBSITE_LOAD_CERTIFICATES`
   * Valor: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   No portal do Azure, salvar as configurações do aplicativo é um processo de duas etapas: Salve a `WEBSITE_LOAD_CERTIFICATES` configuração chave-valor e, em seguida, selecione o botão **salvar** na parte superior da folha.
1. Selecione as configurações de **TLS/SSL** do aplicativo. Selecione **certificados de chave privada (. pfx)** . Use o processo **importar Key Vault certificado** duas vezes para importar o certificado do site para comunicação HTTPS e o Identity certificado de autenticação de tokens do servidor autoassinado do site.
1. Navegue até a folha **domínios personalizados** . No site do registrador de domínio, use o **endereço IP** e a **ID de verificação de domínio personalizado** para configurar o domínio. Uma configuração de domínio típica inclui:
   * Um **registro a** com um **host** de `@` e um valor do endereço IP do portal do Azure.
   * Um **registro txt** com um **host** de `asuid` e o valor da ID de verificação gerado pelo Azure e fornecidos pelo portal do Azure.

   Certifique-se de salvar as alterações no site do registrador de domínio corretamente. Alguns sites do registrador exigem um processo de duas etapas para salvar registros de domínio: um ou mais registros são salvos individualmente, atualizando o registro do domínio com um botão separado.
1. Retorne para a folha **domínios personalizados** no portal do Azure. Selecione **Adicionar domínio personalizado** . Selecione a opção **um registro** . Forneça o domínio e selecione **validar** . Se os registros de domínio estiverem corretos e propagados pela Internet, o portal permitirá que você selecione o botão **Adicionar domínio personalizado** .

   Pode levar alguns dias para que as alterações no registro do domínio se propaguem entre os servidores de nomes de domínio da Internet (DNS) depois que eles forem processados pelo seu registrador de domínio. Se os registros de domínio não forem atualizados dentro de três dias úteis, confirme se os registros estão definidos corretamente com o registrador de domínios e entre em contato com o atendimento ao cliente.
1. Na folha **domínios personalizados** , o **estado do SSL** para o domínio é marcado `Not Secure` . Selecione o link **Adicionar Associação** . Selecione o certificado HTTPS do site do cofre de chaves para a associação de domínio personalizada.
1. No Visual Studio, abra o arquivo de configurações do aplicativo do projeto do *servidor* ( `appsettings.json` ou `appsettings.Production.json` ). Na Identity configuração do servidor, adicione a seção a seguir `Key` . Especifique a **entidade** do certificado autoassinado para a `Name` chave. No exemplo a seguir, o nome comum do certificado atribuído no cofre de chaves é `IdentityServerSigning` , que gera um **assunto** de `CN=IdentityServerSigning` :

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. No Visual Studio, crie um perfil de [publicação](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) de serviço Azure app para o projeto de *servidor* . Na barra de menus, selecione: **Compilar**  >  **publicar**  >  **novo**  >  **Azure**  >  **serviço de Azure app** do Azure (Windows ou Linux). Quando o Visual Studio está conectado a uma assinatura do Azure, você pode definir a **exibição** de recursos do Azure por **tipo de recurso** . Navegue dentro da lista de **aplicativos Web** para localizar o serviço de aplicativo para o aplicativo e selecioná-lo. Selecione **Concluir** .
1. Quando o Visual Studio retorna para a janela de **publicação** , as dependências do cofre de chaves e do serviço de banco de dados SQL Server são detectadas automaticamente.

   Nenhuma alteração de configuração nas configurações padrão é necessária para o serviço de cofre de chaves.

   Para fins de teste, o banco de dados [SQLite](https://www.sqlite.org/index.html) local de um aplicativo, que é configurado por padrão pelo Blazor modelo, pode ser implantado com o aplicativo sem configuração adicional. A configuração de um banco de dados diferente para o Identity servidor em produção está além do escopo deste artigo. Para obter mais informações, consulte os recursos de banco de dados nos seguintes conjuntos de documentação:
   * [Serviço de Aplicativo](/azure/app-service/)
   * [Identity Servidor](https://identityserver4.readthedocs.io/en/latest/)

1. Selecione o link **Editar** sob o nome do perfil de implantação na parte superior da janela. Altere a URL de destino para a URL de domínio personalizado do site (por exemplo, `https://www.contoso.com` ). Salve as configurações.
1. Publique o aplicativo. O Visual Studio abre uma janela do navegador e solicita o site em seu domínio personalizado.

A documentação do Azure contém detalhes adicionais sobre como usar os serviços do Azure e domínios personalizados com a associação TLS no serviço de aplicativo, incluindo informações sobre como usar registros CNAME em vez de registros. Para obter mais informações, consulte os seguintes recursos:

* [Documentação do serviço de aplicativo](/azure/app-service/)
* [Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](/azure/app-service/configure-ssl-bindings)
* [Cofre da Chave do Azure](/azure/key-vault/)

É recomendável usar uma nova janela de navegador em particular ou Incognito para cada execução de teste de aplicativo após uma alteração no aplicativo, configuração de aplicativo ou serviços do Azure no portal do Azure. cookieOs s remanescentes de uma execução de teste anterior podem resultar em falha na autenticação ou autorização ao testar o site mesmo quando a configuração do site está correta. Para obter mais informações sobre como configurar o Visual Studio para abrir uma nova janela de navegador em particular ou Incognito para cada execução de teste, consulte a seção [ Cookie s e dados do site](#cookies-and-site-data) .

Quando a configuração do serviço de aplicativo é alterada na portal do Azure, as atualizações geralmente entram em vigor rapidamente, mas não são instantâneas. Às vezes, você deve aguardar um curto período para que um serviço de aplicativo seja reiniciado para que uma alteração de configuração entre em vigor.

Se estiver solucionando um problema de carregamento de certificado, execute o seguinte comando em um shell de comando portal do Azure [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell. O comando fornece uma lista de certificados que o aplicativo pode acessar do `My`  >  `CurrentUser` repositório de certificados. A saída inclui entidades de certificado e impressões digitais úteis ao depurar um aplicativo:

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Implantação no serviço Azure App](xref:security/authentication/identity/spa#deploy-to-production)
* [Importar um certificado do Key Vault (documentação do Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
