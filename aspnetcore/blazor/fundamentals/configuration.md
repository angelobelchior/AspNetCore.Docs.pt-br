---
title: Configuração de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre a configuração de Blazor aplicativos, incluindo configurações de aplicativo, autenticação e configuração de log.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 1607a80ea12cedd82b5996cf1b09e02e1aada2fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628801"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Configuração de ASP.NET Core Blazor

> [!NOTE]
> Este tópico aplica-se a Blazor WebAssembly . Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/configuration/index> .

Blazor WebAssembly carrega a configuração de arquivos de configurações do aplicativo por padrão:

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

Outros provedores de configuração registrados pelo aplicativo também podem fornecer configuração.

Nem todos os provedores ou recursos de provedor são apropriados para Blazor WebAssembly aplicativos:

* [Azure Key Vault provedor de configuração](xref:security/key-vault-configuration): o provedor não tem suporte para identidade gerenciada e ID do aplicativo (ID do cliente) com cenários de segredo do cliente. A ID do aplicativo com um segredo do cliente não é recomendada para nenhum aplicativo ASP.NET Core, especialmente Blazor WebAssembly aplicativos porque o segredo do cliente não pode ser protegido do lado do cliente para acessar o serviço.
* [Provedor de configuração de Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app): o provedor não é apropriado para Blazor WebAssembly aplicativos porque os Blazor WebAssembly aplicativos não são executados em um servidor no Azure.

> [!WARNING]
> A configuração em um Blazor WebAssembly aplicativo é visível para os usuários. **Não armazene os segredos ou as credenciais do aplicativo na configuração.**

Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configuração de configurações do aplicativo

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a>Provedor de configuração personalizada com EF Core

O provedor de configuração personalizada com EF Core demonstrado no <xref:fundamentals/configuration/index#custom-configuration-provider> funciona com Blazor WebAssembly aplicativos.

Adicione o provedor de configuração do exemplo com o seguinte código em `Program.Main` ( `Program.cs` ):

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>Fonte de configuração de memória

O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para fornecer configuração adicional:

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

Para ler outros arquivos de configuração da `wwwroot` pasta para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo. Ao usar essa abordagem, o <xref:System.Net.Http.HttpClient> registro de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>Configuração de autenticação

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuração de log

Adicione uma referência de pacote para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) :

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).

`wwwroot/appsettings.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuração do host Builder

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuração armazenada em cache

Os arquivos de configuração são armazenados em cache para uso offline. Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação. A edição de arquivos de configuração entre implantações não tem efeito porque:

* Os usuários têm versões em cache dos arquivos que eles continuam a usar.
* Os `service-worker.js` arquivos e do PWA `service-worker-assets.js` devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.

Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .
