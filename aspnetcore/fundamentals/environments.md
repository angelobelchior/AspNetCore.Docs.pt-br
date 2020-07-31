---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330598"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="900aa-103">Usar vários ambientes no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="900aa-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="900aa-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="900aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="900aa-105">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="900aa-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="900aa-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="900aa-107">Ambientes</span><span class="sxs-lookup"><span data-stu-id="900aa-107">Environments</span></span>

<span data-ttu-id="900aa-108">Para determinar o ambiente de tempo de execução, o ASP.NET Core lê as seguintes variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="900aa-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="900aa-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="900aa-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="900aa-110">`ASPNETCORE_ENVIRONMENT`Quando <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="900aa-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="900aa-111">A chamada de modelos de aplicativo Web ASP.NET Core padrão `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="900aa-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="900aa-112">O `ASPNETCORE_ENVIRONMENT` valor é substituído `DOTNET_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="900aa-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="900aa-113">`IHostEnvironment.EnvironmentName`pode ser definido como qualquer valor, mas os seguintes valores são fornecidos pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="900aa-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="900aa-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: O [launchSettings.jsem](#lsj) conjuntos de arquivos `ASPNETCORE_ENVIRONMENT` para `Development` no computador local.</span><span class="sxs-lookup"><span data-stu-id="900aa-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="900aa-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: O padrão if `DOTNET_ENVIRONMENT` e `ASPNETCORE_ENVIRONMENT` não foram definidos.</span><span class="sxs-lookup"><span data-stu-id="900aa-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="900aa-116">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="900aa-116">The following code:</span></span>

* <span data-ttu-id="900aa-117">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="900aa-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="900aa-118">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido como `Staging` , `Production` ou `Staging_2` .</span><span class="sxs-lookup"><span data-stu-id="900aa-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="900aa-119">Injeta <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="900aa-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="900aa-120">Essa abordagem é útil quando o aplicativo requer apenas o ajuste `Startup.Configure` para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="900aa-121">É semelhante ao código gerado pelos modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="900aa-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="900aa-122">O [auxiliar de marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de [IHostEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) para incluir ou excluir marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="900aa-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="900aa-123">A [página About](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) do [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) inclui a marcação anterior e exibe o valor de `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="900aa-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="900aa-124">No Windows e no macOS, variáveis de ambiente e valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="900aa-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="900aa-125">Variáveis e valores de ambiente do Linux diferenciam **maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="900aa-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="900aa-126">Criar EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="900aa-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="900aa-127">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) usado neste documento é baseado em um Razor projeto de páginas chamado *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="900aa-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="900aa-128">O código a seguir cria e executa um aplicativo Web chamado *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="900aa-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="900aa-129">Quando o aplicativo é executado, ele exibe alguns dos seguintes resultados:</span><span class="sxs-lookup"><span data-stu-id="900aa-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="900aa-130">Desenvolvimento e launchSettings.jsem</span><span class="sxs-lookup"><span data-stu-id="900aa-130">Development and launchSettings.json</span></span>

<span data-ttu-id="900aa-131">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="900aa-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="900aa-132">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="900aa-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="900aa-133">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="900aa-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="900aa-134">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="900aa-135">O *launchSettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="900aa-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="900aa-136">É usado somente no computador de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="900aa-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="900aa-137">Não está implantado.</span><span class="sxs-lookup"><span data-stu-id="900aa-137">Is not deployed.</span></span>
* <span data-ttu-id="900aa-138">contém configurações de perfil.</span><span class="sxs-lookup"><span data-stu-id="900aa-138">contains profile settings.</span></span>

<span data-ttu-id="900aa-139">O JSON a seguir mostra o *launchSettings.jsno* arquivo para um projeto Web ASP.NET Core chamado *EnvironmentsSample* criado com o Visual Studio ou `dotnet new` :</span><span class="sxs-lookup"><span data-stu-id="900aa-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="900aa-140">A marcação anterior contém dois perfis:</span><span class="sxs-lookup"><span data-stu-id="900aa-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="900aa-141">`IIS Express`: O perfil padrão usado ao iniciar o aplicativo no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="900aa-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="900aa-142">A `"commandName"` chave tem o valor `"IISExpress"` , portanto, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) é o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="900aa-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="900aa-143">Você pode definir o perfil de inicialização para o projeto ou qualquer outro perfil incluído.</span><span class="sxs-lookup"><span data-stu-id="900aa-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="900aa-144">Por exemplo, na imagem abaixo, selecionar o nome do projeto inicia o [servidor Web Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="900aa-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![IIS Express iniciar no menu](environments/_static/iisx2.png)
* <span data-ttu-id="900aa-146">`EnvironmentsSample`: O nome do perfil é o nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="900aa-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="900aa-147">Esse perfil é usado por padrão ao iniciar o aplicativo com o `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="900aa-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="900aa-148">A `"commandName"` chave tem o valor `"Project"` , portanto, o [servidor Web Kestrel](xref:fundamentals/servers/kestrel) é iniciado.</span><span class="sxs-lookup"><span data-stu-id="900aa-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="900aa-149">O valor de `commandName` pode especificar o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="900aa-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="900aa-150">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="900aa-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="900aa-151">`IISExpress`: Inicia IIS Express.</span><span class="sxs-lookup"><span data-stu-id="900aa-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="900aa-152">`IIS`: Nenhum servidor Web foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="900aa-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="900aa-153">Espera-se que o IIS esteja disponível.</span><span class="sxs-lookup"><span data-stu-id="900aa-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="900aa-154">`Project`: Inicia o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="900aa-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="900aa-155">A guia **depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o *launchSettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="900aa-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="900aa-156">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="900aa-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="900aa-157">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="900aa-159">O seguinte *launchSettings.jsno* arquivo contém vários perfis:</span><span class="sxs-lookup"><span data-stu-id="900aa-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="900aa-160">Os perfis podem ser selecionados:</span><span class="sxs-lookup"><span data-stu-id="900aa-160">Profiles can be selected:</span></span>

* <span data-ttu-id="900aa-161">Na interface do usuário do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="900aa-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="900aa-162">Usando o [`dotnet run`](/dotnet/core/tools/dotnet-run) comando em um shell de comando com a `--launch-profile` opção definida como o nome do perfil.</span><span class="sxs-lookup"><span data-stu-id="900aa-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="900aa-163">*Essa abordagem dá suporte apenas a perfis Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="900aa-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="900aa-164">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="900aa-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="900aa-165">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="900aa-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="900aa-166">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="900aa-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="900aa-167">O exemplo a seguir define várias [variáveis de ambiente de valores de configuração de host](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="900aa-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="900aa-168">O *. vscode/launch.jsno* arquivo é usado somente pelo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="900aa-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="900aa-169">Produção</span><span class="sxs-lookup"><span data-stu-id="900aa-169">Production</span></span>

<span data-ttu-id="900aa-170">O ambiente de produção deve ser configurado para maximizar a segurança, o [desempenho](xref:performance/performance-best-practices)e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="900aa-171">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="900aa-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="900aa-172">[Caching](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="900aa-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="900aa-173">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="900aa-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="900aa-174">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="900aa-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="900aa-175">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="900aa-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="900aa-176">Monitoramento e [log](xref:fundamentals/logging/index) de produção habilitados.</span><span class="sxs-lookup"><span data-stu-id="900aa-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="900aa-177">Por exemplo, usando [Application insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="900aa-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="900aa-178">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-178">Set the environment</span></span>

<span data-ttu-id="900aa-179">Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="900aa-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="900aa-180">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="900aa-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="900aa-181">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="900aa-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="900aa-182">Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="900aa-183">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="900aa-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="900aa-184">A [página About](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) do [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) exibe o valor de `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="900aa-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="900aa-185">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="900aa-185">Azure App Service</span></span>

<span data-ttu-id="900aa-186">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="900aa-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="900aa-187">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="900aa-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="900aa-188">No grupo **configurações** , selecione a folha **configuração** .</span><span class="sxs-lookup"><span data-stu-id="900aa-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="900aa-189">Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="900aa-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="900aa-190">Na janela **Adicionar/Editar configuração de aplicativo** , forneça `ASPNETCORE_ENVIRONMENT` o **nome**.</span><span class="sxs-lookup"><span data-stu-id="900aa-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="900aa-191">Para **valor**, forneça o ambiente (por exemplo, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="900aa-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="900aa-192">Marque a caixa de seleção **configuração do slot de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="900aa-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="900aa-193">Para obter mais informações, consulte [configurar ambientes de preparo no serviço de Azure app](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="900aa-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="900aa-194">Selecione **OK** para fechar a janela **Adicionar/Editar configuração de aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="900aa-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="900aa-195">Selecione **salvar** na parte superior da folha de **configuração** .</span><span class="sxs-lookup"><span data-stu-id="900aa-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="900aa-196">Azure App serviço reinicia automaticamente o aplicativo depois que uma configuração de aplicativo é adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="900aa-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="900aa-197">Windows</span><span class="sxs-lookup"><span data-stu-id="900aa-197">Windows</span></span>

<span data-ttu-id="900aa-198">Valores de ambiente em *launchSettings.jsem valores de* substituição definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="900aa-199">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="900aa-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="900aa-200">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="900aa-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="900aa-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="900aa-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="900aa-202">O comando anterior define `ASPNETCORE_ENVIRONMENT` somente para processos iniciados a partir dessa janela de comando.</span><span class="sxs-lookup"><span data-stu-id="900aa-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="900aa-203">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="900aa-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="900aa-204">Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="900aa-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="900aa-207">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="900aa-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="900aa-208">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="900aa-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="900aa-209">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="900aa-210">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="900aa-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="900aa-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="900aa-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="900aa-212">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="900aa-213">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="900aa-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="900aa-214">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="900aa-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="900aa-215">Valores de ambiente em *launchSettings.jsem valores de* substituição definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="900aa-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="900aa-216">**web.config**</span></span>

<span data-ttu-id="900aa-217">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="900aa-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="900aa-218">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="900aa-218">**Project file or publish profile**</span></span>

<span data-ttu-id="900aa-219">**Para implantações do Windows IIS:** Inclua a `<EnvironmentName>` propriedade no [perfil de publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="900aa-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="900aa-220">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="900aa-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="900aa-221">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="900aa-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="900aa-222">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="900aa-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="900aa-223">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="900aa-224">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="900aa-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="900aa-225">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="900aa-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="900aa-226">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="900aa-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="900aa-227">macOS</span><span class="sxs-lookup"><span data-stu-id="900aa-227">macOS</span></span>

<span data-ttu-id="900aa-228">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="900aa-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="900aa-229">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="900aa-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="900aa-230">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="900aa-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="900aa-231">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="900aa-231">Edit the file using any text editor.</span></span> <span data-ttu-id="900aa-232">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="900aa-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="900aa-233">Linux</span><span class="sxs-lookup"><span data-stu-id="900aa-233">Linux</span></span>

<span data-ttu-id="900aa-234">Para distribuições do Linux, use o `export` comando em um prompt de comando para configurações de variáveis baseadas em sessão e *bash_profile* arquivo para configurações de ambiente de nível de máquina.</span><span class="sxs-lookup"><span data-stu-id="900aa-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="900aa-235">Definir o ambiente no código</span><span class="sxs-lookup"><span data-stu-id="900aa-235">Set the environment in code</span></span>

<span data-ttu-id="900aa-236">Chame <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="900aa-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="900aa-237">Consulte <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="900aa-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="900aa-238">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-238">Configuration by environment</span></span>

<span data-ttu-id="900aa-239">Para carregar a configuração por ambiente, consulte <xref:fundamentals/configuration/index#json-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="900aa-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="900aa-240">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="900aa-241">Injetar IWebHostEnvironment na classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="900aa-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="900aa-242">Injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` Construtor.</span><span class="sxs-lookup"><span data-stu-id="900aa-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="900aa-243">Essa abordagem é útil quando o aplicativo requer configuração `Startup` para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="900aa-244">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="900aa-244">In the following example:</span></span>

* <span data-ttu-id="900aa-245">O ambiente é mantido no `_env` campo.</span><span class="sxs-lookup"><span data-stu-id="900aa-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="900aa-246">`_env`é usado no `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="900aa-247">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="900aa-247">Startup class conventions</span></span>

<span data-ttu-id="900aa-248">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="900aa-249">O aplicativo pode definir várias `Startup` classes para ambientes diferentes.</span><span class="sxs-lookup"><span data-stu-id="900aa-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="900aa-250">A `Startup` classe apropriada está selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="900aa-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="900aa-251">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="900aa-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="900aa-252">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="900aa-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="900aa-253">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="900aa-254">Aplicativos típicos não precisarão dessa abordagem.</span><span class="sxs-lookup"><span data-stu-id="900aa-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="900aa-255">Para implementar classes baseadas em ambiente `Startup` , crie `Startup{EnvironmentName}` classes e uma classe de fallback `Startup` :</span><span class="sxs-lookup"><span data-stu-id="900aa-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="900aa-256">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="900aa-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="900aa-257">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="900aa-257">Startup method conventions</span></span>

<span data-ttu-id="900aa-258">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="900aa-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="900aa-259">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente:</span><span class="sxs-lookup"><span data-stu-id="900aa-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="900aa-260">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="900aa-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="900aa-261">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="900aa-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="900aa-262">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="900aa-263">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="900aa-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="900aa-264">Ambientes</span><span class="sxs-lookup"><span data-stu-id="900aa-264">Environments</span></span>

<span data-ttu-id="900aa-265">O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="900aa-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="900aa-266">`ASPNETCORE_ENVIRONMENT`pode ser definido como qualquer valor, mas três valores são fornecidos pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="900aa-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="900aa-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="900aa-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="900aa-268">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="900aa-268">The preceding code:</span></span>

* <span data-ttu-id="900aa-269">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="900aa-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="900aa-270">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="900aa-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="900aa-271">O [auxiliar de marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="900aa-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="900aa-272">No Windows e no macOS, variáveis de ambiente e valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="900aa-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="900aa-273">Variáveis e valores de ambiente do Linux diferenciam maiúsculas de minúsculas por padrão.</span><span class="sxs-lookup"><span data-stu-id="900aa-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="900aa-274">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="900aa-274">Development</span></span>

<span data-ttu-id="900aa-275">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="900aa-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="900aa-276">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="900aa-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="900aa-277">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="900aa-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="900aa-278">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="900aa-279">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="900aa-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="900aa-280">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="900aa-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="900aa-281">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="900aa-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="900aa-282">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="900aa-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="900aa-283">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="900aa-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="900aa-284">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="900aa-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="900aa-285">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="900aa-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="900aa-286">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="900aa-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="900aa-287">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="900aa-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="900aa-288">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="900aa-289">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="900aa-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="900aa-290">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="900aa-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="900aa-291">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="900aa-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="900aa-293">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="900aa-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="900aa-294">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="900aa-295">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="900aa-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="900aa-296">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="900aa-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="900aa-297">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="900aa-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="900aa-298">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="900aa-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="900aa-299">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="900aa-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="900aa-300">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="900aa-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="900aa-301">Produção</span><span class="sxs-lookup"><span data-stu-id="900aa-301">Production</span></span>

<span data-ttu-id="900aa-302">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="900aa-303">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="900aa-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="900aa-304">Armazenamento em cache.</span><span class="sxs-lookup"><span data-stu-id="900aa-304">Caching.</span></span>
* <span data-ttu-id="900aa-305">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="900aa-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="900aa-306">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="900aa-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="900aa-307">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="900aa-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="900aa-308">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="900aa-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="900aa-309">Por exemplo, [Application insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="900aa-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="900aa-310">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-310">Set the environment</span></span>

<span data-ttu-id="900aa-311">Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="900aa-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="900aa-312">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="900aa-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="900aa-313">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="900aa-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="900aa-314">Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="900aa-315">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="900aa-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="900aa-316">Configuração de plataforma ou variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="900aa-317">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="900aa-317">Azure App Service</span></span>

<span data-ttu-id="900aa-318">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="900aa-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="900aa-319">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="900aa-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="900aa-320">No grupo **configurações** , selecione a folha **configuração** .</span><span class="sxs-lookup"><span data-stu-id="900aa-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="900aa-321">Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="900aa-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="900aa-322">Na janela **Adicionar/Editar configuração de aplicativo** , forneça `ASPNETCORE_ENVIRONMENT` o **nome**.</span><span class="sxs-lookup"><span data-stu-id="900aa-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="900aa-323">Para **valor**, forneça o ambiente (por exemplo, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="900aa-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="900aa-324">Marque a caixa de seleção **configuração do slot de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="900aa-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="900aa-325">Para obter mais informações, consulte [configurar ambientes de preparo no serviço de Azure app](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="900aa-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="900aa-326">Selecione **OK** para fechar a janela **Adicionar/Editar configuração de aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="900aa-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="900aa-327">Selecione **salvar** na parte superior da folha de **configuração** .</span><span class="sxs-lookup"><span data-stu-id="900aa-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="900aa-328">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="900aa-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="900aa-329">Windows</span><span class="sxs-lookup"><span data-stu-id="900aa-329">Windows</span></span>

<span data-ttu-id="900aa-330">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="900aa-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="900aa-331">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="900aa-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="900aa-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="900aa-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="900aa-333">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="900aa-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="900aa-334">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="900aa-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="900aa-335">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="900aa-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="900aa-336">Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="900aa-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="900aa-339">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="900aa-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="900aa-340">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="900aa-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="900aa-341">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="900aa-342">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="900aa-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="900aa-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="900aa-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="900aa-344">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="900aa-345">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="900aa-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="900aa-346">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="900aa-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="900aa-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="900aa-347">**web.config**</span></span>

<span data-ttu-id="900aa-348">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="900aa-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="900aa-349">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="900aa-349">**Project file or publish profile**</span></span>

<span data-ttu-id="900aa-350">**Para implantações do Windows IIS:** Inclua a `<EnvironmentName>` propriedade no [perfil de publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="900aa-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="900aa-351">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="900aa-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="900aa-352">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="900aa-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="900aa-353">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="900aa-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="900aa-354">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="900aa-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="900aa-355">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="900aa-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="900aa-356">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="900aa-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="900aa-357">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="900aa-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="900aa-358">macOS</span><span class="sxs-lookup"><span data-stu-id="900aa-358">macOS</span></span>

<span data-ttu-id="900aa-359">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="900aa-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="900aa-360">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="900aa-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="900aa-361">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="900aa-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="900aa-362">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="900aa-362">Edit the file using any text editor.</span></span> <span data-ttu-id="900aa-363">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="900aa-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="900aa-364">Linux</span><span class="sxs-lookup"><span data-stu-id="900aa-364">Linux</span></span>

<span data-ttu-id="900aa-365">Para distribuições do Linux, use o `export` comando em um prompt de comando para configurações de variáveis baseadas em sessão e *bash_profile* arquivo para configurações de ambiente de nível de máquina.</span><span class="sxs-lookup"><span data-stu-id="900aa-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="900aa-366">Definir o ambiente no código</span><span class="sxs-lookup"><span data-stu-id="900aa-366">Set the environment in code</span></span>

<span data-ttu-id="900aa-367">Chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="900aa-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="900aa-368">Consulte <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="900aa-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="900aa-369">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-369">Configuration by environment</span></span>

<span data-ttu-id="900aa-370">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="900aa-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="900aa-371">*appSettings* arquivos (*appSettings. { Ambiente}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="900aa-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="900aa-372">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="900aa-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="900aa-373">Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="900aa-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="900aa-374">Veja <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="900aa-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="900aa-375">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="900aa-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="900aa-376">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="900aa-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="900aa-377">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="900aa-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="900aa-378">Injetar IHostingEnvironment em Startup.Configlhar</span><span class="sxs-lookup"><span data-stu-id="900aa-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="900aa-379">Injetar <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="900aa-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="900aa-380">Essa abordagem é útil quando o aplicativo requer apenas a configuração `Startup.Configure` para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="900aa-381">Injetar IHostingEnvironment na classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="900aa-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="900aa-382">Injetar <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> no `Startup` Construtor e atribuir o serviço a um campo para uso em toda a `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="900aa-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="900aa-383">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="900aa-384">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="900aa-384">In the following example:</span></span>

* <span data-ttu-id="900aa-385">O ambiente é mantido no `_env` campo.</span><span class="sxs-lookup"><span data-stu-id="900aa-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="900aa-386">`_env`é usado no `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="900aa-387">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="900aa-387">Startup class conventions</span></span>

<span data-ttu-id="900aa-388">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="900aa-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="900aa-389">O aplicativo pode definir `Startup` classes separadas para ambientes diferentes (por exemplo, `StartupDevelopment` ).</span><span class="sxs-lookup"><span data-stu-id="900aa-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="900aa-390">A `Startup` classe apropriada está selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="900aa-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="900aa-391">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="900aa-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="900aa-392">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="900aa-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="900aa-393">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="900aa-394">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="900aa-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="900aa-395">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="900aa-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="900aa-396">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="900aa-396">Startup method conventions</span></span>

<span data-ttu-id="900aa-397">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="900aa-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="900aa-398">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="900aa-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="900aa-399">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="900aa-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
