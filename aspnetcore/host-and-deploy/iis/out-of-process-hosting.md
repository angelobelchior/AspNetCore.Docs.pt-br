---
title: Hospedagem fora do processo com IIS e ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a hospedagem fora do processo com o IIS e o módulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 048a18349de4d784ae4abb33bd86a2d9c08c609d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755233"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="66702-103">Hospedagem fora do processo com IIS e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66702-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="66702-104">Como ASP.NET Core aplicativos são executados em um processo separado do processo de trabalho do IIS, o módulo ASP.NET Core lida com o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="66702-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="66702-105">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="66702-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="66702-106">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="66702-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="66702-107">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="66702-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core no cenário de hospedagem fora do processo](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="66702-109">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="66702-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="66702-110">O driver roteia as solicitações para o IIS na porta configurada do site.</span><span class="sxs-lookup"><span data-stu-id="66702-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="66702-111">A porta configurada geralmente é 80 (HTTP) ou 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="66702-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="66702-112">O módulo encaminha as solicitações para Kestrel em uma porta aleatória para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="66702-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="66702-113">A porta aleatória não é 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="66702-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="66702-114">O módulo ASP.NET Core especifica a porta por meio de uma variável de ambiente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="66702-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="66702-115">A <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extensão configura o servidor a ser escutado `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="66702-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="66702-116">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="66702-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="66702-117">O módulo não dá suporte ao encaminhamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66702-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="66702-118">As solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS sobre HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66702-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="66702-119">Depois que o Kestrel pega a solicitação do módulo, a solicitação é encaminhada para o pipeline de middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66702-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="66702-120">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="66702-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="66702-121">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="66702-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="66702-122">A resposta do aplicativo é passada de volta para o IIS, que o encaminha de volta para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="66702-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="66702-123">Para orientação sobre a configuração do Módulo do ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="66702-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="66702-124">Para saber mais sobre hospedagem, confira [Host no ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="66702-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="66702-125">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="66702-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="66702-126">Habilitar os componentes de IISIntegration</span><span class="sxs-lookup"><span data-stu-id="66702-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="66702-127">Ao criar um host no `CreateHostBuilder` ( `Program.cs` ), chame <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> para habilitar a integração do IIS:</span><span class="sxs-lookup"><span data-stu-id="66702-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="66702-128">Para obter mais informações sobre o `CreateDefaultBuilder`, consulte <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="66702-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="66702-129">**Modelo de hospedagem de fora do processo**</span><span class="sxs-lookup"><span data-stu-id="66702-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="66702-130">Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="66702-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="66702-131">O exemplo a seguir impede que o aplicativo preencha `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="66702-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="66702-132">Opção</span><span class="sxs-lookup"><span data-stu-id="66702-132">Option</span></span>                         | <span data-ttu-id="66702-133">Padrão</span><span class="sxs-lookup"><span data-stu-id="66702-133">Default</span></span> | <span data-ttu-id="66702-134">Configuração</span><span class="sxs-lookup"><span data-stu-id="66702-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="66702-135">Se `true`, o [middleware de integração do IIS](#enable-the-iisintegration-components) define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="66702-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="66702-136">Se `false`, o middleware fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="66702-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="66702-137">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="66702-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="66702-138">Saiba mais no tópico [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="66702-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="66702-139">Configura o nome de exibição mostrado aos usuários em páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="66702-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="66702-140">Se `true` e o cabeçalho da solicitação `MS-ASPNETCORE-CLIENTCERT` estiverem presentes, o `HttpContext.Connection.ClientCertificate` será populado.</span><span class="sxs-lookup"><span data-stu-id="66702-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="66702-141">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="66702-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="66702-142">O [middleware de integração do IIS](#enable-the-iisintegration-components) e o módulo ASP.NET Core são configurados para encaminhar o:</span><span class="sxs-lookup"><span data-stu-id="66702-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="66702-143">Esquema (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="66702-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="66702-144">Endereço IP remoto em que a solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="66702-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="66702-145">O [middleware de integração do IIS](#enable-the-iisintegration-components) configura o middleware de cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="66702-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="66702-146">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="66702-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="66702-147">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="66702-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="66702-148">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="66702-148">Out-of-process hosting model</span></span>

<span data-ttu-id="66702-149">Para configurar um aplicativo para hospedagem fora do processo, defina o valor da `<AspNetCoreHostingModel>` propriedade como `OutOfProcess` no arquivo de projeto ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="66702-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="66702-150">A hospedagem em processo é definida com `InProcess` , que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="66702-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="66702-151">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="66702-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="66702-152">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="66702-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="66702-153">Para o fora do processo, o [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> para:</span><span class="sxs-lookup"><span data-stu-id="66702-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="66702-154">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66702-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="66702-155">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="66702-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="66702-156">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="66702-156">Process name</span></span>

<span data-ttu-id="66702-157">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="66702-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="66702-158">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="66702-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="66702-159">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="66702-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="66702-160">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="66702-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="66702-161">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="66702-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="66702-162">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="66702-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="66702-163">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="66702-163">Forward Windows authentication tokens.</span></span>
