---
title: Hospedagem em processo com IIS e ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a hospedagem em processo com o IIS e o módulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058474"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="5fe3d-103">Hospedagem em processo com IIS e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fe3d-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="5fe3d-104">A hospedagem em processo executa um aplicativo ASP.NET Core no mesmo processo que o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="5fe3d-105">A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="5fe3d-106">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Módulo do ASP.NET Core no cenário de hospedagem em processo](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="5fe3d-108">Habilitar hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="5fe3d-108">Enable in-process hosting</span></span>

<span data-ttu-id="5fe3d-109">Desde ASP.NET Core 3,0, a hospedagem em processo foi habilitada por padrão para todos os aplicativos implantados no IIS.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="5fe3d-110">Para configurar explicitamente um aplicativo para hospedagem em processo, defina o valor da `<AspNetCoreHostingModel>` propriedade como `InProcess` no arquivo de projeto ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="5fe3d-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="5fe3d-111">Arquitetura geral</span><span class="sxs-lookup"><span data-stu-id="5fe3d-111">General architecture</span></span>

<span data-ttu-id="5fe3d-112">O fluxo geral de uma solicitação é o seguinte:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="5fe3d-113">A solicitação chega da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="5fe3d-114">O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="5fe3d-115">O módulo ASP.NET Core recebe a solicitação nativa e a passa para o servidor HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="5fe3d-116">O servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="5fe3d-117">Depois que o servidor HTTP IIS processar a solicitação:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="5fe3d-118">A solicitação é enviada para o pipeline de middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="5fe3d-119">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="5fe3d-120">A resposta do aplicativo é retornada ao IIS por meio do Servidor HTTP do IIS.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="5fe3d-121">O IIS enviará a resposta ao cliente que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="5fe3d-122">`CreateDefaultBuilder` Adiciona uma <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instância chamando o <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> método para inicializar o [CoreCLR](/dotnet/standard/glossary#coreclr) e hospedar o aplicativo dentro do processo de trabalho do IIS ( `w3wp.exe` ou `iisexpress.exe` ).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="5fe3d-123">Os testes de desempenho indicam que hospedar um aplicativo em processo do .NET Core oferece uma de taxa de transferência de solicitação significativamente mais elevada em comparação a hospedar o aplicativo fora do processo e enviar por proxy as solicitações para o [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5fe3d-124">Aplicativos publicados como um único arquivo executável não podem ser carregados pelo modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5fe3d-125">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="5fe3d-125">Application configuration</span></span>

<span data-ttu-id="5fe3d-126">Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="5fe3d-127">O exemplo a seguir desabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="5fe3d-128">Opção</span><span class="sxs-lookup"><span data-stu-id="5fe3d-128">Option</span></span> | <span data-ttu-id="5fe3d-129">Padrão</span><span class="sxs-lookup"><span data-stu-id="5fe3d-129">Default</span></span> | <span data-ttu-id="5fe3d-130">Configuração</span><span class="sxs-lookup"><span data-stu-id="5fe3d-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="5fe3d-131">Se `true`, o Servidor do IIS define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5fe3d-132">Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5fe3d-133">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5fe3d-134">Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="5fe3d-135">Configura o nome de exibição mostrado aos usuários em páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="5fe3d-136">Se a e/s síncrona é permitida para o `HttpContext.Request` e o `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="5fe3d-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="5fe3d-137">Obtém ou define o tamanho máximo do corpo da solicitação para o `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="5fe3d-138">Observe que o próprio IIS tem o limite `maxAllowedContentLength` que será processado antes de `MaxRequestBodySize` definido no `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="5fe3d-139">Alterar `MaxRequestBodySize` não afetará `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="5fe3d-140">Para aumentar `maxAllowedContentLength` , adicione uma entrada no `web.config` para definir `maxAllowedContentLength` como um valor mais alto.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="5fe3d-141">Para obter mais detalhes, confira [Configuração](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="5fe3d-142">Diferenças entre a hospedagem em processo e fora do processo</span><span class="sxs-lookup"><span data-stu-id="5fe3d-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="5fe3d-143">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5fe3d-144">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="5fe3d-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5fe3d-145">Para o em processo, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> para:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="5fe3d-146">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5fe3d-147">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5fe3d-148">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5fe3d-149">O [ `requestTimeout` atributo](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5fe3d-150">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5fe3d-151">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-151">Use one app pool per app.</span></span>

* <span data-ttu-id="5fe3d-152">A arquitetura (número de bit) do aplicativo e o runtime instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="5fe3d-153">Por exemplo, os aplicativos publicados para 32 bits (x86) devem ter 32 bits habilitado para seus pools de aplicativos do IIS.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="5fe3d-154">Para obter mais informações, consulte a seção [criar o site do IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="5fe3d-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="5fe3d-155">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-155">Client disconnects are detected.</span></span> <span data-ttu-id="5fe3d-156">O [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) token de cancelamento é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5fe3d-157">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5fe3d-158">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="5fe3d-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5fe3d-159">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="5fe3d-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="5fe3d-160">Não há suporte para [implantações de pacote da Web (arquivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) .</span><span class="sxs-lookup"><span data-stu-id="5fe3d-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
