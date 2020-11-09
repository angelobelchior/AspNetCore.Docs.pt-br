---
title: 'Ambientes de ASP.NET Core Blazor'
author: guardrex
description: 'Saiba mais sobre ambientes no Blazor , incluindo como definir o ambiente de um Blazor WebAssembly aplicativo.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056030"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="d66c8-103">Ambientes de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d66c8-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="d66c8-104">Este tópico aplica-se a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d66c8-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="d66c8-105">Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="d66c8-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d66c8-106">Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d66c8-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="d66c8-107">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="d66c8-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="d66c8-108">Um Blazor WebAssembly aplicativo hospedado pega o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d66c8-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="d66c8-109">O valor do cabeçalho é o ambiente.</span><span class="sxs-lookup"><span data-stu-id="d66c8-109">The value of the header is the environment.</span></span> <span data-ttu-id="d66c8-110">O Blazor aplicativo hospedado e o aplicativo de servidor compartilham o mesmo ambiente.</span><span class="sxs-lookup"><span data-stu-id="d66c8-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="d66c8-111">Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="d66c8-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d66c8-112">Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d66c8-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="d66c8-113">Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d66c8-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="d66c8-114">No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao `web.config` arquivo publicado.</span><span class="sxs-lookup"><span data-stu-id="d66c8-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="d66c8-115">O `web.config` arquivo está localizado na `bin/Release/{TARGET FRAMEWORK}/publish` pasta:</span><span class="sxs-lookup"><span data-stu-id="d66c8-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="d66c8-116">Para usar um `web.config` arquivo personalizado para o IIS que não é substituído quando o aplicativo é publicado na `publish` pasta, consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="d66c8-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="d66c8-117">Obtenha o ambiente do aplicativo em um componente injetando <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e lendo a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d66c8-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="d66c8-118">Durante a inicialização, o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expõe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> por meio da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:</span><span class="sxs-lookup"><span data-stu-id="d66c8-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="d66c8-119">Os métodos de extensão de conveniência a seguir permitem verificar o ambiente atual quanto a desenvolvimento, produção, preparação e nomes de ambiente personalizados:</span><span class="sxs-lookup"><span data-stu-id="d66c8-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="d66c8-120">A <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriedade pode ser usada durante a inicialização quando o <xref:Microsoft.AspNetCore.Components.NavigationManager> serviço não está disponível.</span><span class="sxs-lookup"><span data-stu-id="d66c8-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d66c8-121">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d66c8-121">Additional resources</span></span>

* <xref:fundamentals/environments>
