---
title: Módulo do ASP.NET Core
author: rick-anderson
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: e16735ec06e6d84118e5949fe56c514015105242
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758574"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="65020-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65020-103">ASP.NET Core Module</span></span>

<span data-ttu-id="65020-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)e [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="65020-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65020-105">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="65020-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="65020-106">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="65020-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="65020-107">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="65020-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="65020-108">Versões do Windows com suporte:</span><span class="sxs-lookup"><span data-stu-id="65020-108">Supported Windows versions:</span></span>

* <span data-ttu-id="65020-109">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="65020-109">Windows 7 or later</span></span>
* <span data-ttu-id="65020-110">Windows Server 2012 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="65020-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="65020-111">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="65020-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="65020-112">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="65020-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="65020-113">O módulo não funciona com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="65020-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="65020-114">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="65020-115">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="65020-115">In-process hosting model</span></span>

<span data-ttu-id="65020-116">ASP.NET Core aplicativos assumem como padrão o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="65020-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="65020-117">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="65020-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="65020-118">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="65020-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="65020-119">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="65020-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="65020-120">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="65020-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="65020-121">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="65020-122">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="65020-123">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="65020-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="65020-124">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="65020-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="65020-125">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-125">Use one app pool per app.</span></span>

* <span data-ttu-id="65020-126">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="65020-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="65020-127">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="65020-128">A arquitetura (número de bit) do aplicativo e o runtime instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="65020-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="65020-129">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="65020-129">Client disconnects are detected.</span></span> <span data-ttu-id="65020-130">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65020-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="65020-131">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="65020-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="65020-132">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="65020-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="65020-133">Chame o método `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="65020-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="65020-134">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="65020-135">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="65020-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="65020-136">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="65020-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="65020-137">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="65020-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="65020-138">Não há suporte para [implantações de pacote da Web (arquivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) .</span><span class="sxs-lookup"><span data-stu-id="65020-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="65020-139">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="65020-139">Out-of-process hosting model</span></span>

<span data-ttu-id="65020-140">Para configurar um aplicativo para hospedagem fora do processo, defina o valor da `<AspNetCoreHostingModel>` propriedade como `OutOfProcess` no arquivo de projeto (*. csproj*):</span><span class="sxs-lookup"><span data-stu-id="65020-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="65020-141">A hospedagem em processo é definida com `InProcess` , que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="65020-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="65020-142">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="65020-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="65020-143">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="65020-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="65020-144">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="65020-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="65020-145">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="65020-146">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="65020-147">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-147">Hosting model changes</span></span>

<span data-ttu-id="65020-148">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="65020-149">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="65020-150">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="65020-151">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="65020-151">Process name</span></span>

<span data-ttu-id="65020-152">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="65020-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="65020-153">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="65020-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="65020-154">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="65020-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="65020-155">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="65020-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="65020-156">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="65020-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="65020-157">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="65020-158">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="65020-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="65020-159">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65020-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="65020-160">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="65020-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="65020-161">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="65020-161">Configuration with web.config</span></span>

<span data-ttu-id="65020-162">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="65020-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="65020-163">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="65020-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="65020-164">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="65020-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="65020-165">A <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriedade é definida como `false` para indicar que as configurações especificadas no [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="65020-166">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="65020-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="65020-167">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="65020-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="65020-168">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="65020-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="65020-169">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="65020-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="65020-170">Atributo</span><span class="sxs-lookup"><span data-stu-id="65020-170">Attribute</span></span> | <span data-ttu-id="65020-171">Descrição</span><span class="sxs-lookup"><span data-stu-id="65020-171">Description</span></span> | <span data-ttu-id="65020-172">Padrão</span><span class="sxs-lookup"><span data-stu-id="65020-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="65020-173">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-173">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-174">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="65020-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="65020-175">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-176">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="65020-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="65020-177">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-178">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="65020-179">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="65020-180">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-180">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-181">Especifica o modelo de hospedagem como em processo ( `InProcess` / `inprocess` ) ou fora do processo ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="65020-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="65020-182">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-183">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="65020-184">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="65020-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="65020-185">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="65020-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="65020-186">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="65020-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="65020-187">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="65020-187">Default: `1`</span></span><br><span data-ttu-id="65020-188">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="65020-188">Min: `1`</span></span><br><span data-ttu-id="65020-189">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="65020-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="65020-190">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="65020-190">Required string attribute.</span></span></p><p><span data-ttu-id="65020-191">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="65020-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="65020-192">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="65020-192">Relative paths are supported.</span></span> <span data-ttu-id="65020-193">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="65020-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="65020-194">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-195">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="65020-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="65020-196">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="65020-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="65020-197">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="65020-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="65020-198">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="65020-198">Default: `10`</span></span><br><span data-ttu-id="65020-199">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-199">Min: `0`</span></span><br><span data-ttu-id="65020-200">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="65020-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="65020-201">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="65020-202">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="65020-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="65020-203">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="65020-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="65020-204">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="65020-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="65020-205">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="65020-206">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="65020-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="65020-207">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="65020-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="65020-208">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="65020-208">Default: `00:02:00`</span></span><br><span data-ttu-id="65020-209">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="65020-209">Min: `00:00:00`</span></span><br><span data-ttu-id="65020-210">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="65020-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="65020-211">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-212">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="65020-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="65020-213">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="65020-213">Default: `10`</span></span><br><span data-ttu-id="65020-214">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-214">Min: `0`</span></span><br><span data-ttu-id="65020-215">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="65020-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="65020-216">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-217">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="65020-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="65020-218">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="65020-218">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="65020-219">Ao hospedar *em processo*: o processo **não** é reiniciado e **não usa a** configuração **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="65020-219">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="65020-220">Ao hospedar *fora do processo*: o módulo tenta reinicializar o processo quando recebe uma nova solicitação e continua tentando reiniciar o processo em solicitações de entrada subsequentes, a menos que o aplicativo não seja iniciado **rapidFailsPerMinute** número de vezes no último minuto de reversão.</span><span class="sxs-lookup"><span data-stu-id="65020-220">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="65020-221">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="65020-221">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="65020-222">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="65020-222">Default: `120`</span></span><br><span data-ttu-id="65020-223">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-223">Min: `0`</span></span><br><span data-ttu-id="65020-224">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="65020-224">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="65020-225">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-225">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-226">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="65020-226">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="65020-227">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-227">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-228">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="65020-228">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="65020-229">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="65020-229">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="65020-230">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="65020-230">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="65020-231">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-231">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="65020-232">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="65020-232">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="65020-233">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="65020-233">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="65020-234">Definir variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="65020-234">Set environment variables</span></span>

<span data-ttu-id="65020-235">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="65020-235">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="65020-236">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="65020-236">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="65020-237">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="65020-237">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="65020-238">O exemplo a seguir define duas variáveis de ambiente em *web.config*. `ASPNETCORE_ENVIRONMENT`configura o ambiente do aplicativo para `Development` .</span><span class="sxs-lookup"><span data-stu-id="65020-238">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="65020-239">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-239">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="65020-240">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-240">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="65020-241">Uma alternativa para definir o ambiente diretamente no *web.config* é incluir a `<EnvironmentName>` propriedade no perfil de [publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="65020-241">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="65020-242">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="65020-242">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="65020-243">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="65020-243">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="65020-244">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="65020-244">app_offline.htm</span></span>

<span data-ttu-id="65020-245">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="65020-245">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="65020-246">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="65020-246">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="65020-247">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="65020-247">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="65020-248">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-248">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="65020-249">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="65020-249">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="65020-250">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-250">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="65020-251">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="65020-251">Start-up error page</span></span>

<span data-ttu-id="65020-252">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-252">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="65020-253">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-253">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="65020-254">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-254">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="65020-255">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-255">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="65020-256">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="65020-256">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="65020-257">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="65020-257">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="65020-258">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="65020-258">Log creation and redirection</span></span>

<span data-ttu-id="65020-259">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="65020-259">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="65020-260">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-260">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="65020-261">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="65020-261">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="65020-262">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="65020-262">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="65020-263">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="65020-263">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="65020-264">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-264">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="65020-265">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-265">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="65020-266">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="65020-266">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="65020-267">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="65020-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="65020-268">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-268">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="65020-269">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo (*.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="65020-269">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="65020-270">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="65020-270">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="65020-271">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="65020-271">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="65020-272">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="65020-272">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="65020-273">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="65020-273">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="65020-274">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="65020-274">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="65020-275">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="65020-275">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="65020-276">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="65020-276">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="65020-277">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-277">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="65020-278">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="65020-278">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="65020-279">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="65020-279">Enhanced diagnostic logs</span></span>

<span data-ttu-id="65020-280">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="65020-280">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="65020-281">Adicione o `<handlerSettings>` elemento ao `<aspNetCore>` elemento em *web.config*. Definir o `debugLevel` como `TRACE` expõe uma maior fidelidade das informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="65020-281">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="65020-282">Todas as pastas no caminho (*logs* no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-282">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="65020-283">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="65020-283">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="65020-284">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="65020-284">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="65020-285">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="65020-285">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="65020-286">ERROR</span><span class="sxs-lookup"><span data-stu-id="65020-286">ERROR</span></span>
* <span data-ttu-id="65020-287">WARNING</span><span class="sxs-lookup"><span data-stu-id="65020-287">WARNING</span></span>
* <span data-ttu-id="65020-288">INFO</span><span class="sxs-lookup"><span data-stu-id="65020-288">INFO</span></span>
* <span data-ttu-id="65020-289">RASTREAMENTO</span><span class="sxs-lookup"><span data-stu-id="65020-289">TRACE</span></span>

<span data-ttu-id="65020-290">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="65020-290">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="65020-291">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="65020-291">CONSOLE</span></span>
* <span data-ttu-id="65020-292">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="65020-292">EVENTLOG</span></span>
* <span data-ttu-id="65020-293">FILE</span><span class="sxs-lookup"><span data-stu-id="65020-293">FILE</span></span>

<span data-ttu-id="65020-294">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="65020-294">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="65020-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="65020-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="65020-296">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="65020-296">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="65020-297">`ASPNETCORE_MODULE_DEBUG`: Configuração de nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="65020-297">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="65020-298">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="65020-298">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="65020-299">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="65020-299">The size of the log isn't limited.</span></span> <span data-ttu-id="65020-300">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-300">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="65020-301">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="65020-301">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="65020-302">Modificar o tamanho da pilha</span><span class="sxs-lookup"><span data-stu-id="65020-302">Modify the stack size</span></span>

<span data-ttu-id="65020-303">*Aplica-se somente ao usar o modelo de hospedagem em processo.*</span><span class="sxs-lookup"><span data-stu-id="65020-303">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="65020-304">Configure o tamanho da pilha gerenciada usando a `stackSize` configuração em bytes em *web.config*. O tamanho padrão é `1048576` bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="65020-304">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="65020-305">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="65020-305">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="65020-306">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="65020-306">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="65020-307">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="65020-307">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="65020-308">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="65020-308">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="65020-309">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="65020-309">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="65020-310">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="65020-310">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="65020-311">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="65020-311">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="65020-312">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="65020-312">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="65020-313">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="65020-313">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="65020-314">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="65020-314">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="65020-315">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-315">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="65020-316">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="65020-316">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="65020-317">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="65020-317">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="65020-318">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="65020-318">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="65020-319">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="65020-319">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="65020-320">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="65020-320">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="65020-321">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-321">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="65020-322">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="65020-322">Run the installer.</span></span>
1. <span data-ttu-id="65020-323">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="65020-323">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="65020-324">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-324">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="65020-325">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-325">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="65020-326">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="65020-326">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="65020-327">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="65020-327">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="65020-328">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="65020-328">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="65020-329">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="65020-329">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="65020-330">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="65020-330">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="65020-331">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\ usuários \\ % username% \\ AppData \\ local \\ Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="65020-331">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="65020-332">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="65020-332">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="65020-333">Módulo</span><span class="sxs-lookup"><span data-stu-id="65020-333">Module</span></span>

<span data-ttu-id="65020-334">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="65020-334">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="65020-335">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-335">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="65020-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="65020-339">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="65020-339">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="65020-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="65020-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="65020-344">Esquema</span><span class="sxs-lookup"><span data-stu-id="65020-344">Schema</span></span>

<span data-ttu-id="65020-345">**IIS**</span><span class="sxs-lookup"><span data-stu-id="65020-345">**IIS**</span></span>

* <span data-ttu-id="65020-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="65020-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="65020-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="65020-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="65020-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="65020-348">**IIS Express**</span></span>

* <span data-ttu-id="65020-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="65020-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="65020-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="65020-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="65020-351">Configuração</span><span class="sxs-lookup"><span data-stu-id="65020-351">Configuration</span></span>

<span data-ttu-id="65020-352">**IIS**</span><span class="sxs-lookup"><span data-stu-id="65020-352">**IIS**</span></span>

* <span data-ttu-id="65020-353">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="65020-353">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="65020-354">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="65020-354">**IIS Express**</span></span>

* <span data-ttu-id="65020-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="65020-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="65020-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="65020-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="65020-357">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="65020-357">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="65020-358">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="65020-358">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="65020-359">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="65020-359">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="65020-360">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="65020-360">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="65020-361">Versões do Windows com suporte:</span><span class="sxs-lookup"><span data-stu-id="65020-361">Supported Windows versions:</span></span>

* <span data-ttu-id="65020-362">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="65020-362">Windows 7 or later</span></span>
* <span data-ttu-id="65020-363">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="65020-363">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="65020-364">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="65020-364">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="65020-365">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="65020-365">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="65020-366">O módulo não funciona com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="65020-366">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="65020-367">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-367">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="65020-368">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="65020-368">In-process hosting model</span></span>

<span data-ttu-id="65020-369">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="65020-369">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="65020-370">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="65020-370">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="65020-371">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="65020-371">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="65020-372">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="65020-372">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="65020-373">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="65020-373">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="65020-374">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="65020-374">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="65020-375">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="65020-375">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="65020-376">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="65020-376">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="65020-377">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-377">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="65020-378">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-378">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="65020-379">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="65020-379">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="65020-380">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="65020-380">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="65020-381">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-381">Use one app pool per app.</span></span>

* <span data-ttu-id="65020-382">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="65020-382">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="65020-383">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-383">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="65020-384">A arquitetura (número de bit) do aplicativo e o runtime instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="65020-384">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="65020-385">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="65020-385">Client disconnects are detected.</span></span> <span data-ttu-id="65020-386">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65020-386">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="65020-387">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="65020-387">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="65020-388">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="65020-388">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="65020-389">Chame o método `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="65020-389">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="65020-390">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-390">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="65020-391">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="65020-391">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="65020-392">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="65020-392">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="65020-393">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="65020-393">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="65020-394">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="65020-394">Out-of-process hosting model</span></span>

<span data-ttu-id="65020-395">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="65020-395">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="65020-396">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="65020-396">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="65020-397">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="65020-397">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="65020-398">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="65020-398">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="65020-399">O valor não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="65020-399">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="65020-400">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="65020-400">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="65020-401">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="65020-401">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="65020-402">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-402">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="65020-403">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-403">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="65020-404">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-404">Hosting model changes</span></span>

<span data-ttu-id="65020-405">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-405">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="65020-406">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-406">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="65020-407">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-407">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="65020-408">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="65020-408">Process name</span></span>

<span data-ttu-id="65020-409">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="65020-409">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="65020-410">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="65020-410">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="65020-411">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="65020-411">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="65020-412">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="65020-412">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="65020-413">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="65020-413">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="65020-414">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-414">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="65020-415">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="65020-415">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="65020-416">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65020-416">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="65020-417">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="65020-417">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="65020-418">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="65020-418">Configuration with web.config</span></span>

<span data-ttu-id="65020-419">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="65020-419">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="65020-420">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="65020-420">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="65020-421">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="65020-421">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="65020-422">A <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriedade é definida como `false` para indicar que as configurações especificadas no [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-422">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="65020-423">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="65020-423">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="65020-424">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="65020-424">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="65020-425">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="65020-425">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="65020-426">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="65020-426">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="65020-427">Atributo</span><span class="sxs-lookup"><span data-stu-id="65020-427">Attribute</span></span> | <span data-ttu-id="65020-428">Descrição</span><span class="sxs-lookup"><span data-stu-id="65020-428">Description</span></span> | <span data-ttu-id="65020-429">Padrão</span><span class="sxs-lookup"><span data-stu-id="65020-429">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="65020-430">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-430">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-431">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="65020-431">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="65020-432">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-432">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-433">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="65020-433">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="65020-434">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-434">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-435">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-435">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="65020-436">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-436">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="65020-437">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-437">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-438">Especifica o modelo de hospedagem como em processo ( `InProcess` / `inprocess` ) ou fora do processo ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="65020-438">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="65020-439">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-439">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-440">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-440">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="65020-441">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="65020-441">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="65020-442">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="65020-442">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="65020-443">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="65020-443">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="65020-444">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="65020-444">Default: `1`</span></span><br><span data-ttu-id="65020-445">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="65020-445">Min: `1`</span></span><br><span data-ttu-id="65020-446">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="65020-446">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="65020-447">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="65020-447">Required string attribute.</span></span></p><p><span data-ttu-id="65020-448">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="65020-448">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="65020-449">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="65020-449">Relative paths are supported.</span></span> <span data-ttu-id="65020-450">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="65020-450">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="65020-451">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-451">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-452">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="65020-452">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="65020-453">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="65020-453">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="65020-454">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="65020-454">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="65020-455">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="65020-455">Default: `10`</span></span><br><span data-ttu-id="65020-456">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-456">Min: `0`</span></span><br><span data-ttu-id="65020-457">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="65020-457">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="65020-458">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-458">Optional timespan attribute.</span></span></p><p><span data-ttu-id="65020-459">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="65020-459">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="65020-460">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="65020-460">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="65020-461">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="65020-461">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="65020-462">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-462">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="65020-463">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="65020-463">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="65020-464">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="65020-464">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="65020-465">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="65020-465">Default: `00:02:00`</span></span><br><span data-ttu-id="65020-466">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="65020-466">Min: `00:00:00`</span></span><br><span data-ttu-id="65020-467">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="65020-467">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="65020-468">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-468">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-469">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="65020-469">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="65020-470">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="65020-470">Default: `10`</span></span><br><span data-ttu-id="65020-471">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-471">Min: `0`</span></span><br><span data-ttu-id="65020-472">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="65020-472">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="65020-473">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-474">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="65020-474">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="65020-475">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="65020-475">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="65020-476">Ao hospedar *em processo*: o processo **não** é reiniciado e **não usa a** configuração **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="65020-476">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="65020-477">Ao hospedar *fora do processo*: o módulo tenta reinicializar o processo quando recebe uma nova solicitação e continua tentando reiniciar o processo em solicitações de entrada subsequentes, a menos que o aplicativo não seja iniciado **rapidFailsPerMinute** número de vezes no último minuto de reversão.</span><span class="sxs-lookup"><span data-stu-id="65020-477">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="65020-478">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="65020-478">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="65020-479">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="65020-479">Default: `120`</span></span><br><span data-ttu-id="65020-480">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-480">Min: `0`</span></span><br><span data-ttu-id="65020-481">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="65020-481">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="65020-482">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-482">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-483">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="65020-483">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="65020-484">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-484">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-485">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="65020-485">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="65020-486">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="65020-486">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="65020-487">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="65020-487">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="65020-488">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-488">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="65020-489">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="65020-489">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="65020-490">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="65020-490">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="65020-491">Configurando variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="65020-491">Setting environment variables</span></span>

<span data-ttu-id="65020-492">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="65020-492">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="65020-493">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="65020-493">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="65020-494">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="65020-494">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="65020-495">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="65020-495">The following example sets two environment variables.</span></span> <span data-ttu-id="65020-496">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="65020-496">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="65020-497">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-497">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="65020-498">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-498">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="65020-499">Uma alternativa para definir o ambiente diretamente no *web.config* é incluir a `<EnvironmentName>` propriedade no perfil de [publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="65020-499">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="65020-500">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="65020-500">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="65020-501">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="65020-501">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="65020-502">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="65020-502">app_offline.htm</span></span>

<span data-ttu-id="65020-503">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="65020-503">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="65020-504">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="65020-504">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="65020-505">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="65020-505">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="65020-506">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-506">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="65020-507">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="65020-507">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="65020-508">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-508">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="65020-509">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="65020-509">Start-up error page</span></span>

<span data-ttu-id="65020-510">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-510">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="65020-511">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-511">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="65020-512">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-512">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="65020-513">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-513">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="65020-514">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="65020-514">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="65020-515">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="65020-515">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="65020-516">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="65020-516">Log creation and redirection</span></span>

<span data-ttu-id="65020-517">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="65020-517">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="65020-518">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-518">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="65020-519">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="65020-519">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="65020-520">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="65020-520">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="65020-521">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="65020-521">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="65020-522">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-522">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="65020-523">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-523">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="65020-524">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="65020-524">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="65020-525">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="65020-525">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="65020-526">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-526">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="65020-527">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo (*.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="65020-527">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="65020-528">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="65020-528">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="65020-529">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="65020-529">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="65020-530">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="65020-530">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="65020-531">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="65020-531">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="65020-532">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="65020-532">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="65020-533">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="65020-533">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="65020-534">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="65020-534">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="65020-535">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="65020-535">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="65020-536">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="65020-536">Enhanced diagnostic logs</span></span>

<span data-ttu-id="65020-537">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="65020-537">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="65020-538">Adicione o `<handlerSettings>` elemento ao `<aspNetCore>` elemento em *web.config*. Definir o `debugLevel` como `TRACE` expõe uma maior fidelidade das informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="65020-538">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="65020-539">As pastas no caminho fornecido para o valor `<handlerSetting>` (*logs* no exemplo anterior) não são criadas automaticamente pelo módulo e devem existir previamente na implantação.</span><span class="sxs-lookup"><span data-stu-id="65020-539">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="65020-540">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="65020-540">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="65020-541">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="65020-541">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="65020-542">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="65020-542">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="65020-543">ERROR</span><span class="sxs-lookup"><span data-stu-id="65020-543">ERROR</span></span>
* <span data-ttu-id="65020-544">WARNING</span><span class="sxs-lookup"><span data-stu-id="65020-544">WARNING</span></span>
* <span data-ttu-id="65020-545">INFO</span><span class="sxs-lookup"><span data-stu-id="65020-545">INFO</span></span>
* <span data-ttu-id="65020-546">RASTREAMENTO</span><span class="sxs-lookup"><span data-stu-id="65020-546">TRACE</span></span>

<span data-ttu-id="65020-547">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="65020-547">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="65020-548">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="65020-548">CONSOLE</span></span>
* <span data-ttu-id="65020-549">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="65020-549">EVENTLOG</span></span>
* <span data-ttu-id="65020-550">FILE</span><span class="sxs-lookup"><span data-stu-id="65020-550">FILE</span></span>

<span data-ttu-id="65020-551">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="65020-551">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="65020-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="65020-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="65020-553">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="65020-553">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="65020-554">`ASPNETCORE_MODULE_DEBUG`: Configuração de nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="65020-554">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="65020-555">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="65020-555">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="65020-556">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="65020-556">The size of the log isn't limited.</span></span> <span data-ttu-id="65020-557">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-557">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="65020-558">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="65020-558">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="65020-559">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="65020-559">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="65020-560">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="65020-560">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="65020-561">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="65020-561">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="65020-562">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="65020-562">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="65020-563">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="65020-563">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="65020-564">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="65020-564">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="65020-565">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="65020-565">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="65020-566">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="65020-566">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="65020-567">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="65020-567">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="65020-568">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="65020-568">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="65020-569">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-569">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="65020-570">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="65020-570">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="65020-571">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="65020-571">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="65020-572">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="65020-572">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="65020-573">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="65020-573">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="65020-574">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="65020-574">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="65020-575">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-575">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="65020-576">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="65020-576">Run the installer.</span></span>
1. <span data-ttu-id="65020-577">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="65020-577">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="65020-578">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-578">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="65020-579">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-579">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="65020-580">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="65020-580">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="65020-581">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="65020-581">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="65020-582">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="65020-582">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="65020-583">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="65020-583">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="65020-584">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="65020-584">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="65020-585">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\ usuários \\ % username% \\ AppData \\ local \\ Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="65020-585">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="65020-586">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="65020-586">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="65020-587">Módulo</span><span class="sxs-lookup"><span data-stu-id="65020-587">Module</span></span>

<span data-ttu-id="65020-588">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="65020-588">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="65020-589">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-589">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="65020-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="65020-593">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="65020-593">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="65020-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="65020-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="65020-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="65020-598">Esquema</span><span class="sxs-lookup"><span data-stu-id="65020-598">Schema</span></span>

<span data-ttu-id="65020-599">**IIS**</span><span class="sxs-lookup"><span data-stu-id="65020-599">**IIS**</span></span>

* <span data-ttu-id="65020-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="65020-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="65020-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="65020-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="65020-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="65020-602">**IIS Express**</span></span>

* <span data-ttu-id="65020-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="65020-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="65020-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="65020-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="65020-605">Configuração</span><span class="sxs-lookup"><span data-stu-id="65020-605">Configuration</span></span>

<span data-ttu-id="65020-606">**IIS**</span><span class="sxs-lookup"><span data-stu-id="65020-606">**IIS**</span></span>

* <span data-ttu-id="65020-607">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="65020-607">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="65020-608">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="65020-608">**IIS Express**</span></span>

* <span data-ttu-id="65020-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="65020-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="65020-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="65020-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="65020-611">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="65020-611">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="65020-612">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="65020-612">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="65020-613">Versões do Windows com suporte:</span><span class="sxs-lookup"><span data-stu-id="65020-613">Supported Windows versions:</span></span>

* <span data-ttu-id="65020-614">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="65020-614">Windows 7 or later</span></span>
* <span data-ttu-id="65020-615">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="65020-615">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="65020-616">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="65020-616">The module only works with Kestrel.</span></span> <span data-ttu-id="65020-617">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="65020-617">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="65020-618">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="65020-618">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="65020-619">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="65020-619">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="65020-620">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="65020-620">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="65020-621">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="65020-621">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="65020-623">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="65020-623">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="65020-624">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="65020-624">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="65020-625">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="65020-625">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="65020-626">O módulo Especifica a porta por meio de uma variável de ambiente na inicialização, e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor a ser escutado `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="65020-626">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="65020-627">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="65020-627">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="65020-628">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="65020-628">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="65020-629">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-629">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="65020-630">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-630">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="65020-631">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="65020-631">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="65020-632">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-632">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="65020-633">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="65020-633">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="65020-634">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="65020-634">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="65020-635">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="65020-635">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="65020-636">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="65020-636">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="65020-637">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65020-637">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="65020-638">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="65020-638">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="65020-639">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65020-639">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="65020-640">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="65020-640">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="65020-641">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="65020-641">Configuration with web.config</span></span>

<span data-ttu-id="65020-642">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="65020-642">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="65020-643">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="65020-643">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="65020-644">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="65020-644">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="65020-645">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="65020-645">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="65020-646">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="65020-646">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="65020-647">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="65020-647">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="65020-648">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="65020-648">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="65020-649">Atributo</span><span class="sxs-lookup"><span data-stu-id="65020-649">Attribute</span></span> | <span data-ttu-id="65020-650">Descrição</span><span class="sxs-lookup"><span data-stu-id="65020-650">Description</span></span> | <span data-ttu-id="65020-651">Padrão</span><span class="sxs-lookup"><span data-stu-id="65020-651">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="65020-652">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-652">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-653">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="65020-653">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="65020-654">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-655">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="65020-655">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="65020-656">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-656">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-657">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-657">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="65020-658">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="65020-658">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="65020-659">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-660">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-660">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="65020-661">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="65020-661">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="65020-662">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="65020-662">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="65020-663">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="65020-663">Default: `1`</span></span><br><span data-ttu-id="65020-664">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="65020-664">Min: `1`</span></span><br><span data-ttu-id="65020-665">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="65020-665">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="65020-666">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="65020-666">Required string attribute.</span></span></p><p><span data-ttu-id="65020-667">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="65020-667">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="65020-668">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="65020-668">Relative paths are supported.</span></span> <span data-ttu-id="65020-669">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="65020-669">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="65020-670">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-670">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-671">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="65020-671">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="65020-672">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="65020-672">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="65020-673">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="65020-673">Default: `10`</span></span><br><span data-ttu-id="65020-674">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-674">Min: `0`</span></span><br><span data-ttu-id="65020-675">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="65020-675">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="65020-676">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-676">Optional timespan attribute.</span></span></p><p><span data-ttu-id="65020-677">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="65020-677">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="65020-678">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="65020-678">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="65020-679">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="65020-679">Default: `00:02:00`</span></span><br><span data-ttu-id="65020-680">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="65020-680">Min: `00:00:00`</span></span><br><span data-ttu-id="65020-681">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="65020-681">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="65020-682">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-682">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-683">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="65020-683">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="65020-684">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="65020-684">Default: `10`</span></span><br><span data-ttu-id="65020-685">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-685">Min: `0`</span></span><br><span data-ttu-id="65020-686">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="65020-686">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="65020-687">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-687">Optional integer attribute.</span></span></p><p><span data-ttu-id="65020-688">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="65020-688">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="65020-689">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="65020-689">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="65020-690">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="65020-690">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="65020-691">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="65020-691">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="65020-692">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="65020-692">Default: `120`</span></span><br><span data-ttu-id="65020-693">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="65020-693">Min: `0`</span></span><br><span data-ttu-id="65020-694">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="65020-694">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="65020-695">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-695">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="65020-696">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="65020-696">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="65020-697">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="65020-697">Optional string attribute.</span></span></p><p><span data-ttu-id="65020-698">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="65020-698">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="65020-699">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="65020-699">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="65020-700">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="65020-700">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="65020-701">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="65020-701">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="65020-702">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="65020-702">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="65020-703">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="65020-703">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="65020-704">Configurando variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="65020-704">Setting environment variables</span></span>

<span data-ttu-id="65020-705">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="65020-705">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="65020-706">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="65020-706">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="65020-707">As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="65020-707">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="65020-708">Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-708">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="65020-709">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="65020-709">The following example sets two environment variables.</span></span> <span data-ttu-id="65020-710">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="65020-710">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="65020-711">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-711">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="65020-712">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-712">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="65020-713">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="65020-713">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="65020-714">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="65020-714">app_offline.htm</span></span>

<span data-ttu-id="65020-715">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="65020-715">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="65020-716">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="65020-716">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="65020-717">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="65020-717">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="65020-718">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-718">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="65020-719">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="65020-719">Start-up error page</span></span>

<span data-ttu-id="65020-720">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="65020-720">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="65020-721">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="65020-721">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="65020-722">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="65020-722">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="65020-724">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="65020-724">Log creation and redirection</span></span>

<span data-ttu-id="65020-725">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="65020-725">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="65020-726">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-726">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="65020-727">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="65020-727">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="65020-728">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="65020-728">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="65020-729">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="65020-729">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="65020-730">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="65020-730">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="65020-731">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65020-731">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="65020-732">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="65020-732">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="65020-733">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="65020-733">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="65020-734">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="65020-734">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="65020-735">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo (*.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="65020-735">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="65020-736">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="65020-736">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="65020-737">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="65020-737">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="65020-738">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="65020-738">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="65020-739">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="65020-739">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="65020-740">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="65020-740">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="65020-741">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65020-741">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="65020-742">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="65020-742">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="65020-743">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="65020-743">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="65020-744">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="65020-744">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="65020-745">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="65020-745">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="65020-746">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="65020-746">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="65020-747">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="65020-747">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="65020-748">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="65020-748">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="65020-749">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="65020-749">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="65020-750">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="65020-750">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="65020-751">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="65020-751">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="65020-752">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-752">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="65020-753">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="65020-753">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="65020-754">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="65020-754">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="65020-755">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="65020-755">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="65020-756">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="65020-756">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="65020-757">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-757">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="65020-758">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="65020-758">Run the installer.</span></span>
1. <span data-ttu-id="65020-759">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="65020-759">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="65020-760">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="65020-760">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="65020-761">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="65020-761">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="65020-762">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="65020-762">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="65020-763">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="65020-763">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="65020-764">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="65020-764">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="65020-765">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="65020-765">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="65020-766">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="65020-766">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="65020-767">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\ usuários \\ % username% \\ AppData \\ local \\ Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="65020-767">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="65020-768">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="65020-768">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="65020-769">Módulo</span><span class="sxs-lookup"><span data-stu-id="65020-769">Module</span></span>

<span data-ttu-id="65020-770">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="65020-770">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="65020-771">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-771">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="65020-773">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="65020-773">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="65020-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="65020-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="65020-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="65020-776">Esquema</span><span class="sxs-lookup"><span data-stu-id="65020-776">Schema</span></span>

<span data-ttu-id="65020-777">**IIS**</span><span class="sxs-lookup"><span data-stu-id="65020-777">**IIS**</span></span>

* <span data-ttu-id="65020-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="65020-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="65020-779">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="65020-779">**IIS Express**</span></span>

* <span data-ttu-id="65020-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="65020-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="65020-781">Configuração</span><span class="sxs-lookup"><span data-stu-id="65020-781">Configuration</span></span>

<span data-ttu-id="65020-782">**IIS**</span><span class="sxs-lookup"><span data-stu-id="65020-782">**IIS**</span></span>

* <span data-ttu-id="65020-783">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="65020-783">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="65020-784">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="65020-784">**IIS Express**</span></span>

* <span data-ttu-id="65020-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="65020-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="65020-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="65020-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="65020-787">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="65020-787">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="65020-788">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="65020-788">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="65020-789">[Fonte de referência do módulo ASP.NET Core (Branch mestre)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use a lista suspensa **Branch** para selecionar uma versão específica (por exemplo, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="65020-789">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
