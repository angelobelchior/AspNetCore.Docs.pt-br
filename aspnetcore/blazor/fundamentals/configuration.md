---
title: 'Configuração de ASP.NET Core Blazor'
author: guardrex
description: 'Saiba mais sobre a configuração de Blazor aplicativos, incluindo configurações de aplicativo, autenticação e configuração de log.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234368"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="dca11-103">Configuração de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="dca11-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="dca11-104">Este tópico aplica-se a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="dca11-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="dca11-105">Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="dca11-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="dca11-106">Blazor WebAssembly carrega a configuração de arquivos de configurações do aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="dca11-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="dca11-107">Outros provedores de configuração registrados pelo aplicativo também podem fornecer configuração.</span><span class="sxs-lookup"><span data-stu-id="dca11-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="dca11-108">Nem todos os provedores ou recursos de provedor são apropriados para Blazor WebAssembly aplicativos:</span><span class="sxs-lookup"><span data-stu-id="dca11-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="dca11-109">[Azure Key Vault provedor de configuração](xref:security/key-vault-configuration): o provedor não tem suporte para identidade gerenciada e ID do aplicativo (ID do cliente) com cenários de segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="dca11-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="dca11-110">A ID do aplicativo com um segredo do cliente não é recomendada para nenhum aplicativo ASP.NET Core, especialmente Blazor WebAssembly aplicativos porque o segredo do cliente não pode ser protegido do lado do cliente para acessar o serviço.</span><span class="sxs-lookup"><span data-stu-id="dca11-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="dca11-111">[Provedor de configuração de Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app): o provedor não é apropriado para Blazor WebAssembly aplicativos porque os Blazor WebAssembly aplicativos não são executados em um servidor no Azure.</span><span class="sxs-lookup"><span data-stu-id="dca11-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="dca11-112">A configuração em um Blazor WebAssembly aplicativo é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="dca11-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="dca11-113">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="dca11-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="dca11-114">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="dca11-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="dca11-115">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="dca11-115">App settings configuration</span></span>

<span data-ttu-id="dca11-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="dca11-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="dca11-117">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="dca11-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="dca11-118">Provedor de configuração personalizada com EF Core</span><span class="sxs-lookup"><span data-stu-id="dca11-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="dca11-119">O provedor de configuração personalizada com EF Core demonstrado no <xref:fundamentals/configuration/index#custom-configuration-provider> funciona com Blazor WebAssembly aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dca11-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="dca11-120">Adicione o provedor de configuração do exemplo com o seguinte código em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="dca11-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="dca11-121">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="dca11-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="dca11-122">Fonte de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="dca11-122">Memory Configuration Source</span></span>

<span data-ttu-id="dca11-123">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para fornecer configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="dca11-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="dca11-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="dca11-124">`Program.Main`:</span></span>

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

<span data-ttu-id="dca11-125">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="dca11-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="dca11-126">Para ler outros arquivos de configuração da `wwwroot` pasta para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="dca11-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="dca11-127">Ao usar essa abordagem, o <xref:System.Net.Http.HttpClient> registro de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dca11-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="dca11-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="dca11-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="dca11-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="dca11-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="dca11-130">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="dca11-130">Authentication configuration</span></span>

<span data-ttu-id="dca11-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="dca11-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="dca11-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="dca11-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="dca11-133">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="dca11-133">Logging configuration</span></span>

<span data-ttu-id="dca11-134">Adicione uma referência de pacote para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) :</span><span class="sxs-lookup"><span data-stu-id="dca11-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="dca11-135">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span><span class="sxs-lookup"><span data-stu-id="dca11-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="dca11-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="dca11-136">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="dca11-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="dca11-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="dca11-138">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="dca11-138">Host builder configuration</span></span>

<span data-ttu-id="dca11-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="dca11-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="dca11-140">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="dca11-140">Cached configuration</span></span>

<span data-ttu-id="dca11-141">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="dca11-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="dca11-142">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="dca11-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="dca11-143">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="dca11-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="dca11-144">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="dca11-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="dca11-145">Os `service-worker.js` arquivos e do PWA `service-worker-assets.js` devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="dca11-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="dca11-146">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="dca11-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
